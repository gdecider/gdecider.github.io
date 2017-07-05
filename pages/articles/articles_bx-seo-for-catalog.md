---
title: Статьи 1С | Вывод SEO блока на страницах каталога товаров
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-seo-for-catalog.html
summary: false
toc: true
---

## Вывод SEO блока на страницах каталога товаров

### Задача

Вывести уникальные для каждой страницы категории и/или товара тексты SEO, а так же метатеги keywords, description и title страницы.

### Решение 0. Утопичное.

Если идет постоянная синхронизация с 1С, и ее данные являются приоритетными, а так же сеошники готовы вносить данные в 1С, то добавляем поля типа "Текст SEO" и прочие туда и выводим их на страницу в нужные места.

Так можно поступить с товарами, но для категорий 1С привязанные тексты не вернет.

### Решение 1. Применяем стандартные возможности Битрикс.

В инфоблок каталога для текста - добавляем свойство для элементов и, если описание категорий не используется, то СЕО текст можно писать туда, иначе - добавляем пользовательское свойство для раздела.

метатеги keywords, description и title страницы заполняются на вкладке "SEO" элементов инфоблок.

### Решение 2. Совмещаем стандартные средства Битрикс и включаемую область.

Для метатегов keywords, description и title страницы используем вкладку "SEO" элементов инфоблок.

Для текста на странице категории/товара используем включаемую область, имя файла должно определяться на основе данных страницы.

```php
<?
// формируем имя файла для включаемой области с сео текстом
$pagenPart = '';
if(isset($_GET['PAGEN_1'])) {
    $pagenPart = $_GET['PAGEN_1'];
}

// область без учета постраничной навигации
$seoFileSuffix = str_replace('/', '_', $arResult['VARIABLES']['SECTION_CODE_PATH']).'_descr_seo';

// область с учетом постраничной навигации
$seoFileSuffixPagen = $seoFileSuffix.$pagenPart;
$seoFilePath = $_SERVER['DOCUMENT_ROOT'].'/catalog/sect_' . $seoFileSuffixPagen . '.php';

// если есть для постранички, то подключим ее
if (file_exists($seoFilePath)) {
    $seoFileSuffix = $seoFileSuffixPagen;
}?>

// подключим область с сео текстом
<?$APPLICATION->IncludeComponent(
	"bitrix:main.include",
	"",
	Array(
		"AREA_FILE_RECURSIVE" => "N",
		"AREA_FILE_SHOW" => "sect",
		"AREA_FILE_SUFFIX" => $seoFileSuffix,
		"EDIT_TEMPLATE" => ""
	)
);?>
```

### Решение 3. Храним данные в отдельном инфоблоке.

Тут есть 2 варианта привязки инфоблока с SEO данными:

  * **1** В символьный "Символьный код" вносим часть URL страницы без домена и получаем выборкой по соответствию.

  * **2** Добавляем в SEO инфоблок свойства "Привязка к элементам инфоблока" и "Привязка к разделам инфоблока", которые будут ссылаться на товары и разделы каталога.

**Пример получения данных на странице категории:**

```php
$intSectionID = $APPLICATION->IncludeComponent(
	"bitrix:catalog.section",
	"",
	array( 
		...
	),
	$component
);?>

// получим данные связанного с категорией сео блока
$arSelect = [
	'NAME',
	'PROPERTY_LINK',
	'PROPERTY_KEYWORDS',
	'PROPERTY_DESCRIPTION',
	'PREVIEW_TEXT',
];

$arFilter = [
	'ACTIVE' => 'Y',
	'IBLOCK_ID' => IBID_SEO_CATALOG,
	'PROPERTY_LINK' => $intSectionID,
];

$arOrder = ['SORT' => 'DESC'];

$dbSeo = CIBlockElement::GetList($arOrder, $arFilter, false, false, $arSelect);
$arSeo = $dbSeo->Fetch();

if($arSeo) {
	$APPLICATION->SetPageProperty("title", $arSeo["NAME"]);
	$APPLICATION->SetPageProperty("keywords", $arSeo["PROPERTY_KEYWORDS_VALUE"]);
	$APPLICATION->SetPageProperty("description", $arSeo["PROPERTY_DESCRIPTION_VALUE"]);
}

// в нужном месте страницы выводим текст
<? if($arSeo && $arSeo["PREVIEW_TEXT"]): ?>
    <?=$arSeo["PREVIEW_TEXT"];?>
<? endif; ?>
```