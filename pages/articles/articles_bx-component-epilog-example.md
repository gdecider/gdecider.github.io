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

При отображении элементов каталога, если элемент есть в корзине посетителя - изменить вид и текст кнопки "в корзину".

### Решение

Как [известно](https://dev.1c-bitrix.ru/learning/course/index.php?COURSE_ID=43&LESSON_ID=2975&LESSON_PATH=3913.4565.2975) 
файл **component_epilog.php** вызывается системой на каждый хит, вне зависимости от кеширования страницы. 
Мы воспользуемся этой возможностью и вызовем в нем отложенные функции, которые установят дополнительный класс для кнопки корзины 
и изменят ее текст, но только для нужных товаров.

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

```php
<? if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

/**
 * @var array $arParams
 * @var array $templateData
 * @var string $templateFolder
 * @var CatalogSectionComponent $component
 */
 
\Bitrix\Main\Diag\Debug::dump($arResult['ITEMS_IDS']);
?>
```
