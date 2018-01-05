---
title: Работа с инфоблоками | Статьи CMS "1С Битрикс"
keywords: инфоблоки 1с битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-iblock-examples.html
summary: false
toc: false
---

## Создание типа инфоблока

```php
<?
```

## Удаление типа инфоблока

```php
<?
```

## Создание инфоблока

```php
<?
```

## Удаление инфоблока

```php
<?
function ibDelete($ibCode, $ibTypeCode) {
    $arOrder = [];
    $arFilter = ['TYPE' => $ibTypeCode, 'CODE' => $ibCode];
    $dbIBList = CIBlock::GetList($arOrder, $arFilter);

    if ($dbIBList->SelectedRowsCount() != 1) {
        return false;
    }
    
    $arIBList = $dbIBList->GetNext();

    $db->StartTransaction();
    if (!CIBlock::Delete($arIBList['ID'])) {
        $db->Rollback();
        return false;
    }
    
    $db->Commit();
    
    return true;
}
```

## Проверка наличия свойства в инфоблоке

```php
<?
function isPropExists($ibId, $propCode) {
    $dbProperties = \CIBlockProperty::GetList([], ["IBLOCK_ID" => $ibId, 'CODE' => $propCode]);

    if ($dbProperties->SelectedRowsCount() > 0) {
        return true;
    }

    return false;
}
```

## Добавление свойства в инфоблок

```php
<?
function addIBlockProp($ibId, $propCode) {
    
    $ibp = new \CIBlockProperty();

    $arFields = Array(
        "NAME" => $propCode,
        "ACTIVE" => "Y",
        "SORT" => 100, // Сортировка
        "CODE" => $propCode,
        "PROPERTY_TYPE" => "S", // Строка
        "ROW_COUNT" => 1, // Количество строк
        "COL_COUNT" => 60, // Количество столбцов
        "IBLOCK_ID" => $ibId
    );
    $propId = $ibp->Add($arFields);

    if (!$propId) {
        return false;
        //\Bitrix\Main\Diag\Debug::dump($ibp->LAST_ERROR);
        //die();
    }

    return true;
}
```

## Добавление данных в инфоблок

```php
<?
```

## Обновление данных в инфоблоке

```php
<?
```
