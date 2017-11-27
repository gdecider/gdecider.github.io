---
title: Статьи 1С Битрикс | Умный фильтр, бизнес-логика сайта и перестроение фасетного индекса
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-smart-filter-faset.html
summary: false
toc: true
---

## Умный фильтр, бизнес-логика сайта и перестроение фасетного индекса

### Задача

[Бизнес-логика](https://ru.wikipedia.org/wiki/%D0%91%D0%B8%D0%B7%D0%BD%D0%B5%D1%81-%D0%BB%D0%BE%D0%B3%D0%B8%D0%BA%D0%B0) не всегда логична 
и иногда не реализуема в рамках инструментария, предоставляемого продуктом, а порой заставляет искать довольно странные обходные пути.

Требуется вывести каталог товаров с установленными параметрами фильтрации:
  * Цена товара должна быть больше 0,
  * Показывать только товары у которых есть изображения,
  * Свойство "Показывать на сайте", которое приходит из 1С должно быть в значении "Да"
  
Так же на странице со списком элементов требуется разместить компонент умного фильтра.

### Решение

На первый взгляд все кажется привычно и просто: размещаем компоненты, передаем нужные ограничения в глобальную переменную, 
которая передается в компонент в качестве фильтра "FILTER_NAME" и все должно заработать.

Проблема с которой мы сталкнемся при таком подходе - **в умном фильтре отображается больше характеристик товара, чем есть товаров 
в выводе каталога**. Связано это с тем, что компонент умного фильтра использует [фасетный индекс](https://dev.1c-bitrix.ru/learning/course/?COURSE_ID=42&LESSON_ID=5364)
для [обеспечения своей быстрой работы](https://dev.1c-bitrix.ru/learning/course/?COURSE_ID=43&LESSON_ID=6923).

Для решения этой проблемы вспомним о том, что **индекс строится только по активным товарам**, поэтому нам нужно:
  * **1)** Деактивировать товары, которые не попадают под условия бизнес-лигики отображения товаров на сайте.
  * **2)** Перестроить фасетный индекс с учетом изменений активности товаров
  * **3)** Повесить обработчик события "окончания синхронизации с 1С", выполняющее пункты 1 и 2.
  
#### Деактивация товаров

Битрикс не предоставляет ф-ии для группового изменения элементов инфоблока. Есть метод [CIBlockElement::Update](https://dev.1c-bitrix.ru/api_help/iblock/classes/ciblockelement/update.php), который может обновлять данные элементов по ID, для нашей задачи он не подходит, т.к. нам нужно деактивировать несколько тысяч товаров, а при работе этого метода дополнительно вызываются стандартные события Битрикса OnStartIBlockElementUpdate, OnAfterIBlockElementUpdate, что так же замедлит процесс деактивации.

Поэтому мы деактивируем товары прямым запросом к базе данных, используя возможности "нового" ядра D7

```php
/**
 * Деактивация элементов инфоблок в соответствии с основным фильтром товаров
 * @param int $iblockId
 * @param array $arFilter
 * @return int
 */
function actualizeProducts($iblockId, $arFilter) {
    if(!\Bitrix\Main\Loader::includeModule('iblock')) {
        return false;
    }
        
    $arFilterFinal = [
        'IBLOCK_ID' => $iblockId,
        $arFilter,
    ];
    
    // получим список элементов для деактивации
    $dbRes = CIBlockElement::GetList([], $arFilterFinal, false, false, ['ID']);

    $arNotValidIds = [];
    while($arItem = $dbRes->getNext()) {
        $arNotValidIds[] = $arItem['ID'];
    }

    $res = 0;
    if(count($arNotValidIds) > 0) {
        $strNotValidIds = implode(',', $arNotValidIds);
        // формируем запрос на обновления флага активности товаров
        $sql = "UPDATE " . \Bitrix\Iblock\ElementTable::getTableName() . " SET ACTIVE = 'N' WHERE ID IN (" . $strNotValidIds . ")";

        $connection = \Bitrix\Main\Application::getConnection();
        $connection->queryExecute($sql);
        // запускаем переиндексацию фасета, это наша кастомная ф-я
        $res = reindexCatalogFaset($iblockId);
    }

    return $res;
}
```

#### Пересчет фасетного индекса

```php
/**
 * @param $iblockId
 * @return int
 */
function reindexCatalogFaset($iblockId) {

    $max_execution_time = 20;

    // Пересоздание фасетного индекса
    // Удалим имеющийся индекс
    Bitrix\Iblock\PropertyIndex\Manager::dropIfExists($iblockId);
    Bitrix\Iblock\PropertyIndex\Manager::markAsInvalid($iblockId);
    
    // Создадим новый индекс
    $index = Bitrix\Iblock\PropertyIndex\Manager::createIndexer($iblockId);
    $index->startIndex();
    $NS = 0;

    do {
        $res = $index->continueIndex($max_execution_time);
        $NS += $res;
    } while($res > 0);

    $index->endIndex();

    // чистим кэши
    \CBitrixComponent::clearComponentCache("bitrix:catalog.smart.filter");
    \CIBlock::clearIblockTagCache($iblockId);

    return $NS;
}
```

#### Установка выполнения деактивации и переиндексации на событие завершения обмена с 1С

После каждого обмена файлом с 1С Битрикс вызывает обработку события [OnSuccessCatalogImport1C](https://dev.1c-bitrix.ru/api_help/catalog/events/onsuccesscatalogimport1c.php). Обращаем внимание на фразу "после каждого обмена **файлом**", т.е. это означает, что если в процессе обмена 1С и Битрикс обменялись 124мя файлами, то и событие OnSuccessCatalogImport1C было вызвано 124 раза.

Такой вариант нас не устраивает, нам нужно знать фактическое окончание обмена с 1С.

Для этого на событие OnSuccessCatalogImport1C повесим установку агента, с интервалом в 5 минут, такой интервал гарантирует нам, что обмен уже завершился, т.к. шаг обмена всегда меньше 5ти минут, а если событие вызовется раньше, то оно перепишет имеющийся агент новым и с новым интервалом выполнения.

Результатом выполнения агента будет создание файла в корне сайта. Этот файл послужит флагом для обработчика в кроне, который 
и запустит процесс пересоздания фасетного индекса.

В файле ```/local/php_interface/include/constants.php``` добавим:

```php
const EXCH_1C_FILE_FLAG_NAME = 'flagExch1CFinished';
```

В файле ```/local/php_interface/include/handlers.php``` добавим:

```php
<?php if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true) die();

$eventManager = \Bitrix\Main\EventManager::getInstance();

$eventManager->addEventHandler("iblock", "OnAfterIBlockElementUpdate", ["CDec1C", "handlerOnAfterIBlockElementUpdate"]);
```

Функции в классе обработчике

```php
<?php
class CDec1C {
    /**
     * Событие завершения обмена файлом в процессе обмена с 1С
     */
    public function handlerOnSuccessCatalogImport1C($arParams, $fileName) {
        CAgent::AddAgent('CDec1C::createExch1CFinishFile();', '', 'N', 300);
    }
    
    /**
     * Создание файла флага завершения обмена с 1С
     */
    public static function createExch1CFinishFile() {
        $siteRoot = realpath(__DIR__ . '/../../../');
	$filePath = $siteRoot .  '/' . EXCH_1C_FILE_FLAG_NAME;

	$fp = fopen($filePath, 'wb');
	fwrite($fp, date('Y.m.d H:i:s'));
	fclose($fp);
    }
}
```

#### Cron файл и добавление задания в crontab

Файлы для добавления в крон будем хранить в папке ```/local/crons/```

создадим файл afterExch1C.php

```php
<?
$_SERVER["DOCUMENT_ROOT"] = realpath(__DIR__ . '/../../');
require($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/prolog_before.php");

$filePath = $_SERVER["DOCUMENT_ROOT"] . '/' . EXCH_1C_FILE_FLAG_NAME;

if (!file_exists($filePath)) {
    return;
}

// сразу удалим файл, что бы не запуститься более 1ого раза
unlink($filePath);

$arFilter = CCommon::CATALOG_MAIN_FILTER_NOTVALID;
echo "индекс пересоздан " . CCommon::actualizeProducts(IBID_CATALOG, $arFilter) . "\n";
?>
```

Добавим задание в крон. Интервал выполнения проверки - каждые 5 минут.

Открываем файл заданий крона на изменение

```bash
crontab -e
```

Добавляем наше задание

```bash
*/5 * * * * <path_to_site>/local/crons/afterExch1C.php # на событие окончания обмена с 1С, выполняется КАЖДЫЕ 5 минут
```
