---
title: Статьи 1С Битрикс | Массовая установка свойства типа "Список" в нужное значение
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx_set_prop_type_list.html
summary: false
toc: false
---

## Массовая установка свойства типа "Список" в нужное значение

### Задача

Требуется установить свойство типа "Список" всем элементам инфоблока в определенное значение.

### Решение

```php
<?
// переменные
$IBLOCK_ID = 14;
$PROP_CODE = 'SAYT_OPTOVIK';
$PROP_VALUE = 'Да';

// найдем код значения св-ва типа "Список"
$dbPropVals = CIBlockProperty::GetPropertyEnum($PROP_CODE, [], ["IBLOCK_ID"=>$IBLOCK_ID, "VALUE"=>$PROP_VALUE]);
$arPropVal = $dbPropVals->GetNext();

\Bitrix\Main\Diag\Debug::dump($arPropVal);

// установим всем элементам с неустановленным свойством значение найденное выше

$arFilter = [
    'IBLOCK_ID' => $IBLOCK_ID,
];

$arSelect = [
    'ID',
    'NAME',
    'PROPERTY_'.$PROP_CODE,
];

$dbEls = CIBlockElement::GetList(['ID' => 'ASC'], $arFilter, false, false, $arSelect);

while ($arEl = $dbEls->GetNext()) {
    if(!$arEl['PROPERTY_'.$PROP_CODE.'_VALUE'] && $arPropVal['ID']) {
        CIBlockElement::SetPropertyValues($arEl['ID'], $IBLOCK_ID, $arPropVal['ID'], $PROP_CODE);
    }
}
?>
```
