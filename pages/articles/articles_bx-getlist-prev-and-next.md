---
title: Статьи 1С Битрикс | Получить предыдущий и следующий элементы
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-getlist-prev-and-next.html
summary: false
toc: false
---

## Получить предыдущий и следующий элементы

### Задача

Выбрать предыдущий и следующий элемент по отношению к текущему, с учетом сортировки

### Решение

```php
<?php
// id инфоблока
$iblockId = 1;
// id элемента для которого ищем соседей
$elId = 31;

// поля для отбора
$arSelect = [
    'ID',
    'DETAIL_PAGE_URL'
];

// фильтрация
$arFilter = [
    'IBLOCK_ID' => $iblockId,
    'ACTIVE' => 'Y',
    'SECTION_GLOBAL_ACTIVE' => 'Y'
];

// сортировка
$arOrder = ['ID' => 'ASC'];

// параметры навигации по выборке - ИМЕННО ЭТА НАСТРОЙКА РЕШАЕТ ЗАДАЧУ
$arNavStartParams = ['nPageSize' => 1, 'nElementID' => $elId];

$dbRes = CIBlockElement::GetList($arOrder, $arFilter, false, $arNavStartParams, $arSelect);

while($arRes = $dbRes->GetNext()){
    \Bitrix\Main\Diag\Debug::dump($arRes);
}
?>
```
