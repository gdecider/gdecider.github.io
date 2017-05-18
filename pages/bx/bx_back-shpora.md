---
title: Back-end Шпаргалка Битрикс
keywords: sample homepage
sidebar: bx_sidebar
permalink: bx_back-shpora.html
summary: false
toc: false
---

## Back-end Шпаргалка Битрикс

* Подключение скриптов и стилей вне шаблона

```php
$assets = \Bitrix\Main\Page\Asset::getInstance();

// CSS
$assets->addCss(SITE_TEMPLATE_PATH . '/css/vendor.min.css');

// JS
$assets->addJs(SITE_TEMPLATE_PATH . '/js/vendor.min.js');

// STRING
$assets->addString("<link href='http://fonts.googleapis.com/css?family=PT+Sans:400&subset=cyrillic' rel='stylesheet' type='text/css'>"); 
```

* Подключение скриптов и стилей в шаблоне

```php
// CSS
$this->addExternalCss("/local/styles.css"); 

// JS
$this->addExternalJS("/local/libs.js"); 
```

* Подключение файлов

```php
// С возможностью правки из пользовательской части
<?$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH.'/inc/popupForms.php');?>

// Без возможности правки из пользовательской части
<?$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH.'/inc/popupForms.php', [], ['SHOW_BORDER' => false]);?>
```

* Стандартная проверка в подключаемых файлах

```php
<?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
```

* Данные текущей страницы

```php
$curPage = $APPLICATION->GetCurPage(true);
```

* Примеры применения констант

```php
<html lang="<?=LANGUAGE_ID?>">

<link rel="shortcut icon" href="<?=SITE_TEMPLATE_PATH?>/favicon/favicon.png">
```

* Вывод заголовка окна браузера

```php
<title><?$APPLICATION->ShowTitle()?></title>
```

* Вывод подключенных скриптов и стилей в head

```php
/**
* BITRIX ->ShowHead()
*/
$APPLICATION->ShowMeta("robots", false);
$APPLICATION->ShowMeta("keywords", false);
$APPLICATION->ShowMeta("description", false);
$APPLICATION->ShowLink("canonical", null);
$APPLICATION->ShowCSS(true);
$APPLICATION->ShowHeadStrings();
$APPLICATION->ShowHeadScripts();
```

* Вывод панели администрирования в пользовательской части

```php
<div id="panel"><?$APPLICATION->ShowPanel();?></div>
```

* Определение принадлежности к странице или разделу

```php
if (!CSite::InDir('/index.php')) :
    // все страницы кроме главной
endif;

if (CSite::InDir('/catalog/')) :
    // все страницы и подразделы раздела каталог
endif;

if (CSite::InDir('/catalog/index.php')) :
    // только основная страница раздела каталог
endif;
```

* Формирование ссылки для логина 

```php
// пример формирование ссылок "Logout" и "Регистрация"

<?if ($USER->IsAuthorized()):?>

 <a href="<?echo $APPLICATION->GetCurPageParam("logout=yes", array(
     "login",
     "logout",
     "register",
     "forgot_password",
     "change_password"));?>">Закончить сеанс (logout)</a>

<?else:?>
 
 <a href="<?echo $APPLICATION->GetCurPageParam("register=yes", array(
     "login",
     "logout",
     "forgot_password",
     "change_password"));?>">Регистрация</a>

<?endif;?>
```