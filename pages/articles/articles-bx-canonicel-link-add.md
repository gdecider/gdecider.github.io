---
title: Добавление canonical link на страницы сайта | Статьи CMS "1С Битрикс"
keywords: canonical битрикс, уникальные страницы, убираем дубли страниц
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-canonicel-link-add.html
summary: false
toc: false
---

### Задача

Добавить на все страницы сайта метатег canonical. Для товаров проверять значение определенного свойства инфоблока, в случае его заполненности значение брать из него.

### Решение

Во первых определим какие страницы у нас есть на сате и как они формируются:

* обычные страницы
* страницы новостей и статей, которые выводятся комплексными компонентами, такими как bitrix:news
* страницы каталога товаров, которые формируются компонентом bitrix:catalog

Для каждого из этих типов страниц нужно сформировать link canonical.

#### Canonical для обычных страниц сайта и страниц новостей

Для обычных страниц сайта сформируем значение тега canonical из данных массива $_SERVER и значения адреса страницы, но учтем, что не все страницы обычные, добавим условие для исключения страниц каталога, т.к. для них формирование тега отличается. Страницы новостей исключать не будем, по условиям задачи они не нуждаются в особой логике.

Этот код нужно поместить в файл **footer.php** шаблона сайта:

 ```php
<?
if (!CSite::InDir('/catalog/')) {
    $curPage = $APPLICATION->GetCurPage(false);
    $canonical = $_SERVER["REQUEST_SCHEME"] . '://' . $_SERVER["HTTP_HOST"] . $curPage;
    $assets->addString('<link rel="canonical" href="' . $canonical . '" />');
}
?>
 ```

#### Canonical для страниц каталога

##### Разделы каталога

Для разделов каталога разместим код получения каноникал в файле result_midifier.php шаблона компонента catalog.section:

```php
<?
$arResult['CSTM']['CANONICAL'] = '';

if (isset($arResult["ORIGINAL_PARAMETERS"]["CURRENT_BASE_PAGE"]) && strlen($arResult["ORIGINAL_PARAMETERS"]["CURRENT_BASE_PAGE"]) > 0) {
    // пытаемся получить каноникал из базовой страницы
    $arResult['CSTM']['CANONICAL'] = $_SERVER["REQUEST_SCHEME"] . '://' . $_SERVER["HTTP_HOST"] . $arResult["ORIGINAL_PARAMETERS"]["CURRENT_BASE_PAGE"];
} elseif (strlen($arResult["SECTION_PAGE_URL"]) > 0) {
    // пытаемся получить каноникал из пути детальной страницы
    $arResult['CSTM']['CANONICAL'] = $_SERVER["REQUEST_SCHEME"] . '://' . $_SERVER["HTTP_HOST"] . $arResult["SECTION_PAGE_URL"];
}

// добавим данные, что бы они были доступны в component_epilog.php
if (is_object($this->__component)) {
    $cp = $this->__component;
    $cp->arResult['CSTM_CANONICAL'] = $arResult['CSTM']['CANONICAL'];
    $cp->SetResultCacheKeys(['CSTM_CANONICAL']);

    if (!isset($arResult['CSTM']['CANONICAL'])) {
        $arResult['CSTM']['CANONICAL'] = $cp->arResult['CSTM_CANONICAL'];
    }
}
?>
```

Добавим вывод сформированного каноникола в файл component_epilog.php шаблона компонента catalog.section:

```php
<?
if (strlen($arResult['CSTM_CANONICAL']) > 0 ) {
    \Bitrix\Main\Page\Asset::getInstance()->addString('<link rel="canonical" href="' . $arResult['CSTM_CANONICAL'] . '" />');
}
?>
```

##### Элементы каталога

Для элементов каталога разместим код получения каноникал в файле result_midifier.php шаблона компонента catalog.element:

```php
<?
$arResult['CSTM']['CANONICAL'] = '';

if (strlen(trim($arResult['PROPERTIES']['CANONICAL_LINK']['VALUE']))) {
    // пытаемся получить каноникал из свойства, которое заполняют в 1с
    $arResult['CSTM']['CANONICAL'] = trim($arResult['PROPERTIES']['CANONICAL_LINK']['VALUE']);
} elseif (isset($arResult["ORIGINAL_PARAMETERS"]["CURRENT_BASE_PAGE"]) && strlen($arResult["ORIGINAL_PARAMETERS"]["CURRENT_BASE_PAGE"]) > 0) {
    // пытаемся получить каноникал из базовой страницы
    $arResult['CSTM']['CANONICAL'] = $_SERVER["REQUEST_SCHEME"] . '://' . $_SERVER["HTTP_HOST"] . $arResult["ORIGINAL_PARAMETERS"]["CURRENT_BASE_PAGE"];
} elseif (strlen($arResult["DETAIL_PAGE_URL"]) > 0) {
    // пытаемся получить каноникал из пути детальной страницы
    $arResult['CSTM']['CANONICAL'] = $_SERVER["REQUEST_SCHEME"] . '://' . $_SERVER["HTTP_HOST"] . $arResult["DETAIL_PAGE_URL"];
}

// добавим данные, что бы они были доступны в component_epilog.php
if (is_object($this->__component)) {
    $cp = $this->__component;
    $cp->arResult['CSTM_CANONICAL'] = $arResult['CSTM']['CANONICAL'];
    $cp->SetResultCacheKeys(['CSTM_CANONICAL']);

    if (!isset($arResult['CSTM']['CANONICAL'])) {
        $arResult['CSTM']['CANONICAL'] = $cp->arResult['CSTM_CANONICAL'];
    }
}
?>
```

Добавим вывод сформированного каноникола в файл component_epilog.php шаблона компонента catalog.element:

```php
<?
if (strlen($arResult['CSTM_CANONICAL']) > 0 ) {
    \Bitrix\Main\Page\Asset::getInstance()->addString('<link rel="canonical" href="' . $arResult['CSTM_CANONICAL'] . '" />');
}
?>
```
