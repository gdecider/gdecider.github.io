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
function ibTypeAdd($ibTypeCode, $hasSections = 'Y', $sort = 500, $lang = 'ru') {
    $ibtCode = strtolower($ibTypeCode);
    $dbIbt = CIBlockType::GetByID($ibtCode);
    $arIbt = $dbIbt->GetNext();

    if($arIbt) {
        return false;
    }

    $arFields = [
        'ID' => $ibtCode,
        'SECTIONS' => $hasSections,
        'IN_RSS' => 'N',
        'SORT' => $sort,
        'LANG' => $lang,
    ];

    $obBlocktype = new \CIBlockType();
    $db->StartTransaction();
    $res = $obBlocktype->Add($arFields);

    if(!$res) {
        $db->Rollback();
        
        return false;
    }
    
    $db->Commit();
    
    return true;
}
```

## Удаление типа инфоблока

```php
<?
function ibTypeDelete($ibTypeCode) {
    $dbIbt = CIBlockType::GetByID($ibTypeCode);
    $arIbt = $dbIbt->GetNext();

    if(!$arIbt) {
        return false;
    }

    $db->StartTransaction();
    if(!CIBlockType::Delete($ibTypeCode)) {
        $db->Rollback();
        return false;
    }
    
    $db->Commit();
        
    return true;
}
```

## Создание инфоблока

```php
<?
function ibAdd($ibName, $ibCode, $ibTypeCode, $arProps) {

	$ib = new CIBlock();
        $arFields = Array(
        "ACTIVE" => 'Y',
        "NAME" => $ibName,
        "CODE" => $ibCode,
        "IBLOCK_TYPE_ID" => $ibTypeCode,
        "SITE_ID" => [SITE_ID],
        "LID" => SITE_ID,
        "SORT" => 1000,
        "WORKFLOW" => 'N',
        //"GROUP_ID" => Array("2"=>"D", "3"=>"R")
    );

    $ibId = $ib->Add($arFields);

    if ($ibId <= 0) {
    	// \Bitrix\Main\Diag\Debug::dump($ib->LAST_ERROR);
        // die();
    	return false;
    }
    
    // добавляем свойства
    foreach ($arProps as $arProp) {

        $dbProperties = CIBlockProperty::GetList([], ["IBLOCK_ID" => $ibId, 'CODE' => $arProp['CODE']]);
        if ($dbProperties->SelectedRowsCount() > 0) {
            continue;
        }

        $ibp = new CIBlockProperty;

        $arFields = Array(
            "NAME" => $arProp['NAME'],
            "ACTIVE" => "Y",
            "SORT" => 100, // Сортировка
            "CODE" => $arProp['CODE'],
            "PROPERTY_TYPE" => "S", // Строка
            "ROW_COUNT" => 1, // Количество строк
            "COL_COUNT" => 60, // Количество столбцов
            "IBLOCK_ID" => $ibId
        );
        $propId = $ibp->Add($arFields);

        if (!$propId) {
            // \Bitrix\Main\Diag\Debug::dump($ibp->LAST_ERROR);
            // die();
            continue;
        }
    }

    return false;

}

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
function ibRowAdd($ibId, $arField, $arProps) {

    $el = new \CIBlockElement();

    $arItem = [
        "IBLOCK_SECTION_ID" => false, // элемент лежит в корне раздела
        "IBLOCK_ID" => $ibId,
        "PROPERTY_VALUES" => $arProps,
        "ACTIVE" => "Y", // активен
    ];

    $arItem = array_merge($arItem, $arField);

    // создаем элемент
    if ( !($ITEM_ID = $el->Add($arItem)) ) {
        return false;
    }

    return true;

}
```

## Обновление данных в инфоблоке

```php
<?
function ibRowUpdate($ibItemId, $arField, $arProps) {

    $el = new \CIBlockElement();

    $arItem = [
        "IBLOCK_SECTION_ID" => false, // элемент лежит в корне раздела
        "PROPERTY_VALUES" => $arProps,
        "ACTIVE" => "Y", // активен
    ];

    $arItem = array_merge($arItem, $arField);

    // обновляем элемент
    if ( !($ITEM_ID = $el->Update($ibItemId, $arItem)) ) {
        return false;
    }

    return true;

}
```

## Удаление данных из инфоблока

```php
<?
function ibRowDelete($ibItemId) {

    return \CIBlockElement::Delete($ibItemId);

}
```
