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

// Через включаемую область
<?$APPLICATION->IncludeComponent(
    "bitrix:main.include",
    "",
    Array(
        "AREA_FILE_SHOW" => "file",
        "AREA_FILE_SUFFIX" => "",
        "EDIT_TEMPLATE" => "",
        "PATH" => SITE_TEMPLATE_PATH."/inc/partName.php"
    )
);?>
```

* Стандартная проверка в подключаемых файлах

```php
<?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
```

* Данные текущей страницы

```php
<?php
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
<?php
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
<?php
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

* Получение объектов приложения, контекста и запроса

```php
<?php
// полный вариант
$application = \Bitrix\Main\Application::getInstance();
$context = $application->getContext();
$request = $context->getRequest();

// короткий вариант получения данных запроса
$request = \Bitrix\Main\Context::getCurrent()->getRequest();

// истпользование объекта запроса

// получение GET параметра
$varName = $request['varName'];

// или тоже самое
$varName = $request->getQuery('varName');

// получение POST параметра
$varName = $request->getPost('varName');

// проверка типа запроса
if ($request->isPost()) {
	echo 'is post request';
}
```

* Подключение модулей

```php
<?php
// Статический метод подключает модуль по его имени.
\Bitrix\Main\Loader::includeModule($moduleName);

// Статический метод подключает партнёрский модуль по его имени.
\Bitrix\Main\Loader::includeSharewareModule($partnerModuleName);
```

* Работа с фреймом

```php
<?
$frame = new \Bitrix\Main\Page\FrameBuffered("city_dynamic");
$frame->begin();

    if(isset($_GET['city'])) {
        $_SESSION['USER_CITY'] = $_GET['city'];
    }
    elseif(!isset($_SESSION['USER_CITY']) || !trim($_SESSION['USER_CITY'])) {
        $_SESSION['USER_CITY'] = CCommon::getCityByIP();
    }?>
    
    <?=$_SESSION['USER_CITY']?>

<?$frame->beginStub();?>
    Определяется....
<?$frame->end();?>
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

```php
<?
// Подключение модуля 
use Bitrix\Main\Loader;

Loader::includeModule("sale");

// или
\Bitrix\Main\Loader::includeModule('sale');
?>
```

* Редирект

```php
<?php
/* LocalRedirect(
 string url,
 bool skip_security_check=false,
 string status="302 Found"
); */

// Простой 302 редирект
LocalRedirect('/some_page/');

// 301 редирект с проверкой модуля безопасности на фишинг
LocalRedirect('/some_page/', false, "301 Moved permanently");

// 301 редирект БЕЗ проверкой модуля безопасности на фишинг
LocalRedirect('/some_page/', true, "301 Moved permanently");
```

* Проверка наличия группы у пользователя

```php
<?php

// Проверка идет на соответствие группам с оператором ИЛИ, т.е. согласно приведенному примеру, под выборку попадут пользователи с группой "4" или "5", в том числе пользователи, состоящие в обеих группах.

if ( CSite::InGroup( array(4,5) ) ) {
    // если указанные группы есть, то выполняем код
}
```