---
title: Статьи 1С Битрикс | Сортировка элементов каталога
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-catalog-sort.html
summary: false
toc: false
---

## Сортировка элементов каталога

### Задача

Вывести ссылки для сортировки элементов каталога и сами элементы каталога отсортированные в нужном порядке.

### Решение

#### PHP

Перед компонентом вывода элементов каталога (bitrix:catalog.section) разместим вывод пунктов для сортировки

```php
<?
// массив полей сортировки
$arSort = [
    'default' => [
        'BX_FIELD_CODE' => 'CATALOG_PRICE_'.PRICE_BASE_ID,
        'TITLE' => 'по цене',
        'ORDER' => 'DESC',
    ],

    'price' => [
        'BX_FIELD_CODE' => 'CATALOG_PRICE_'.PRICE_BASE_ID,
        'TITLE' => 'по цене',
        'ORDER' => 'ASC',
    ],

    'name' => [
        'BX_FIELD_CODE' => 'NAME',
        'TITLE' => 'по названию',
        'ORDER' => 'ASC',
    ],
];

// получим текущую сортировку
$request = \Bitrix\Main\Application::getInstance()->getContext()->getRequest();
$currentSort = $request->getQuery('sort');
// если пуст параметр сортировки или в массиве сортировок нет переданной, то применим сортировку по умолчанию
if ( !($currentSort && isset($arSort[$currentSort])) ) {
    $currentSort = 'default';
}

// получим направление сортировки
$order = $request->getQuery('order');
if($arSort[$currentSort]['ORDER'] === $order) {
    $arSort[$currentSort]['ORDER'] = ($arSort[$currentSort]['ORDER'] === 'ASC') ? 'DESC' : 'ASC';
}

// переопределим настройки сортировки для передачи в компонент
$arParams["ELEMENT_SORT_FIELD"] = $arSort[$currentSort]['BX_FIELD_CODE'];
$arParams["ELEMENT_SORT_ORDER"] = ($arSort[$currentSort]['ORDER'] === 'ASC') ? 'DESC' : 'ASC';
?>

<div class="sort">Сортировать:
    <? foreach ($arSort as $key => $sort) :
        if($key === 'default') { continue; } ?>
    <a href="<?echo $APPLICATION->GetCurPageParam("sort={$key}&order={$sort['ORDER']}", array(
        "sort",
        "order",
        ));?>" class="<?=($key === $currentSort) ? 'active' : ''?> "><?=$sort['TITLE']?></a>
    <? endforeach; ?>
</div>

```

