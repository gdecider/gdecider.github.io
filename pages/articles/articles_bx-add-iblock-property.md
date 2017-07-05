---
title: Статьи 1С Битрикс | Добавление свойства в инфоблок
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-add-iblock-property.html
summary: false
toc: false
---

## Добавление свойства в инфоблок

### Задача

Добавить в инфоблок новое свойство, предварительно проверив на существование.

### Решение

#### Типы свойств

```
S - строка, 
N - число, 
F - файл, 
L - список, 
E - привязка к элементам, 
G - привязка к группам.

Для пользовательских типов свойств нужно указывать еще и USER_TYPE.

Привязка к элементам в виде списка:
PROPERTY_TYPE => 'E'
USER_TYPE => 'EList'

Дата/Время:
PROPERTY_TYPE => 'S'
USER_TYPE => 'DateTime'

Привязка к элементам по XML_ID:
PROPERTY_TYPE => 'S'
USER_TYPE => 'ElementXmlID'

Привязка к файлу на сервере:
PROPERTY_TYPE => 'S'
USER_TYPE => 'FileMan'

HTML/текст:
PROPERTY_TYPE => 'S'
USER_TYPE => 'HTML'

Привязка к элементам с автозаполнением:
PROPERTY_TYPE => 'E'
USER_TYPE => 'EAutocomplete'

Привязка к товарам SKU:
PROPERTY_TYPE => 'E'
USER_TYPE => 'SKU'

Счетчик:
PROPERTY_TYPE => 'N'
USER_TYPE => 'Sequence'

Привязка к картам Google Maps:
PROPERTY_TYPE => 'S'
USER_TYPE => 'map_google'

Привязка к Яндекс.Карте:
PROPERTY_TYPE => 'S'
USER_TYPE => 'map_yandex'

Видео:
PROPERTY_TYPE => 'S'
USER_TYPE => 'video'

Привязка к теме форума:
PROPERTY_TYPE => 'S'
USER_TYPE => 'TopicID'

Привязка к пользователю:
PROPERTY_TYPE => 'S'
USER_TYPE => 'UserID'
```

#### Пример добавления

```php
<?
// константа должна быть в файле констант, не забываем все значимое хранить там
const IBID_CATALOG = 1;

// начальные данные
$propCode = 'ACTION_DATE_END';

// Попытка получить свойство по коду
$dbRes = CIBlockProperty::GetList(["ID"=>"ASC"], [
	"IBLOCK_ID"=>IBID_CATALOG, 
	"CODE" => $propCode
]);

$propExists = false;

if ($prop = $dbRes->GetNext()) {
    $propExists = true;
}

// Если свойства нет, то добавим его
if (!$propExists) {
	// Добавляется свойство типа "ДатаВремя"
	$arFields = Array(
        "NAME" => "Дата окончания акции",
        "ACTIVE" => "Y",
        "SORT" => "6000",
        "CODE" => $propCode,
        "PROPERTY_TYPE" => 'S',
        "USER_TYPE" => 'DateTime',
        "IBLOCK_ID" => IBID_CATALOG,
    );
	

	// Если нужно наполнить данными
    $arFields["VALUES"][0] = Array(
        "VALUE" => new \Bitrix\Main\Type\DateTime(),
        "DEF" => "N",
        "SORT" => "100"
    );

    $arFields["VALUES"][1] = Array(
        "VALUE" => \Bitrix\Main\Type\DateTime::createFromUserTime("16.08.2014 15:30:10"),
        "DEF" => "N",
        "SORT" => "200"
    );

    $arFields["VALUES"][2] = Array(
        "VALUE" => \Bitrix\Main\Type\DateTime::createFromTimestamp(strtotime("+5 days"));,
        "DEF" => "Y",
        "SORT" => "300"
    );

    $ibp = new CIBlockProperty();
    $propID = $ibp->Add($arFields);
}
?>
```