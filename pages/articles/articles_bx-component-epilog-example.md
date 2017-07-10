---
title: Статьи 1С Битрикс | Пример работы с component_epilog.php
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-component-epilog-example.html
summary: false
toc: true
---

## Пример работы с component_epilog.php

### Задача

При отображении элементов каталога, если элемент есть в корзине посетителя - изменить вид и текст кнопки "в корзину",
то же выполнить и для товаров в списке сравнения.

### Решение

Как [известно](https://dev.1c-bitrix.ru/learning/course/index.php?COURSE_ID=43&LESSON_ID=2975&LESSON_PATH=3913.4565.2975) 
файл **component_epilog.php** вызывается системой на каждый хит, вне зависимости от кеширования страницы. 
Мы воспользуемся этой возможностью и вызовем в нем отложенные функции, которые установят дополнительный класс для кнопки корзины 
и изменят ее текст, но только для нужных товаров.

#### Подготовка шаблона

Пример вывода кнопки "Купить". В местах вывода текста и дополнительного класса расставим метки для передачи в них значений через механизм отложенных функций.

```php
<button class="add-to-cart jsAddToCart <?$APPLICATION->ShowProperty('CART_BTN_CLASS_'.$arItem['ID']);?>"
        data-prod-id="<?=$arItem['ID']?>"
        data-url="<?=SITE_TEMPLATE_PATH?>/ajax/addToCart.php">
            <?$APPLICATION->ShowProperty('CART_BTN_TEXT_'.$arItem['ID']);?>
</button>
```

Пример вывода кнопки "В сравнение"

```php
<a href="<?=SITE_TEMPLATE_PATH?>/ajax/addToCompare.php"
   data-prod-id="<?=$arItem['ID']?>"
   class="add-to-compare jsToggleCompare <?$APPLICATION->ShowProperty('COMPARE_BTN_CLASS_'.$arItem['ID']);?>"><?$APPLICATION->ShowProperty('COMPARE_BTN_TEXT_'.$arItem['ID']);?></a>
```

#### Пример передачи данных в component_epilog.php из result_modifier.php

В component_epilog нужно передовать только данные с которыми Вы планируете работать, лишние данные увеличивают размер файлов кэша 
и их парсинг системой может быть куда более затратной операцией, чем обращение к БД.

```php
<? if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

/**
 * @var CBitrixComponentTemplate $this
 * @var CatalogSectionComponent $component
 */
 
// соберем коды товаров для передачи в component_epilog.php
$arItemsIds = [];
foreach ($arResult['ITEMS'] as &$arItem) {
    $arItemsIds[] = $arItem['ID'];
}

// добавим данные, что бы они были доступны в component_epilog.php
// ITEMS_IDS - наш уникальный ключ массива $arResult, который и будет передан в файл эпилога
$keyName = 'ITEMS_IDS';
if (is_object($this->__component)) {
    $cp = $this->__component;
    $cp->arResult[$keyName] = $arItemsIds;
    $cp->SetResultCacheKeys(array($keyName));

    if (!isset($arResult[$keyName])) {
        $arResult[$keyName] = $cp->arResult[$keyName];
    }
}
```

#### Пример использования данных в component_epilog.php

В примере использована ф-я модуля local.common, это модуль с набором оберток над стандартными методами АПИ Битрикса, для ускорения процесса разработки.

```php
<? if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

/**
 * @var array $arParams
 * @var array $templateData
 * @var string $templateFolder
 * @var CatalogSectionComponent $component
 */

// соберем массив ID элементов сравнения
$arCompareItemsIds = [];
if (isset($_SESSION["CATALOG_COMPARE_LIST"][IBID_CATALOG]["ITEMS"])) {
    foreach ($_SESSION["CATALOG_COMPARE_LIST"][IBID_CATALOG]["ITEMS"] as $key => $cmprItem) {
        $arCompareItemsIds[] = $key;
    }
}

// получим данные корзины посетителя
// соберем массив ID товаров в корзине
$arCartItemsIds = [];
$arCartInfo = \Local\Common\Cart::getInstance()->getInfo();
foreach ($arCartInfo['items'] as $cartItem) {
    $arCartItemsIds[] = $cartItem['PRODUCT_ID'];
}

foreach($arResult['ITEMS_IDS'] as $id) {
    $cartBtnText = 'в корзину';
    $cartBtnClass = '';
    if (in_array($id, $arCartItemsIds)) {
        $cartBtnText = 'в корзине';
        $cartBtnClass = 'active';
    }
    // Выведем текст и класс кнопки "в корзину"
    $APPLICATION->SetPageProperty('CART_BTN_TEXT_'.$id, $cartBtnText);
    $APPLICATION->SetPageProperty('CART_BTN_CLASS_'.$id, $cartBtnClass);

    $compareBtnText = 'Добавить к сравнению';
    $compareBtnClass = '';
    if(in_array($id, $arCompareItemsIds)) {
        $compareBtnClass = 'active';
        $compareBtnText = 'Удалить из сравнения';
    }
    // Выведем текст и класс кнопки "в сравнение"
    $APPLICATION->SetPageProperty('COMPARE_BTN_TEXT_'.$id, $compareBtnText);
    $APPLICATION->SetPageProperty('COMPARE_BTN_CLASS_'.$id, $compareBtnClass);
}
?>
```
