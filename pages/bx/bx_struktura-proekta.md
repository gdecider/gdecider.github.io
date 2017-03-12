---
title: Структура проекта
keywords: sample homepage
sidebar: bx_sidebar
permalink: bx_struktura-proekta.html
summary: false
toc: false
---
## Структура проекта на CMS Битрикс

```
/var/www/some-site #site root directory
├── /.git
├── /.idea
├── /public
│   ├── /... папки разделов сайта ...
│   ├── /bitrix
│   │   ├── /.git
│   │   ├── /php_interface
│   │   │   ├── dbconn.php
│   │   │   └── dbconn.example.php
│   │   ├── .gitignore
│   │   ├── .settings.php
│   │   ├── .settings.example.php
│   │   └── ... папки и страницы ядра ...
│   ├── /upload
│   ├── /local
│   │   ├── /activities
│   │   ├── /modules
│   │   │   └── /local.common
│   │   ├── /components
│   │   ├── /php_interface
│   │   │   ├── /include
│   │   │   │   ├── constants.php
│   │   │   │   ├── handlers.php
│   │   │   │   └── site_closed.php
│   │   │   ├── init.php
│   │   │   └── this_site_support.php
│   │   ├── /gadgets
│   │   ├── /logs
│   │   ├── /verstka
│   │   │   ├── /.git
│   │   │   └── /.gitignore
│   │   └── /templates
│   │       └── mainTpl
│   │           ├── /ajax
│   │           ├── /components
│   │           ├── /css
│   │           ├── /fonts
│   │           │   └── /font-family-name
│   │           ├── /favicon
│   │           ├── /img
│   │           ├── /include
│   │           ├── /page_templates
│   │           │   └── .content.php
│   │           ├── /sass
│   │           │   ├── /parts
│   │           │   │   ├── _mixins.scss
│   │           │   │   ├── _fonts.scss
│   │           │   │   ├── _common.scss
│   │           │   │   ├── _page-main.scss
│   │           │   │   └── _page-404.scss
│   │           │   └── style.scss
│   │           ├── /.sass-cache
│   │           ├── /js
│   │           │   ├── /vendors
│   │           │   ├── partCommon.js
│   │           │   └── partMain.js
│   │           ├── header.php
│   │           ├── footer.php
│   │           ├── template_styles.php
│   │           ├── styles.css
│   │           ├── .styles.php
│   │           └── description.php
│   ├── ... страницы сайта ...
│   ├── index.php
│   ├── urlrewrite.php
│   ├── .access.php
│   ├── .htaccess
│   ├── .menu-name.menu.php
│   ├── .menu-name.menu_ext.php
│   ├── robots.txt
│   ├── robots.example.txt
│   └── 404.php
├── .gitmodules
└── .gitignore
```

activities — действия БП;
components — компоненты;
gadgets — гаджеты рабочего стола;
modules — модули;
php_interface — init.php, папка user_lang;
templates — шаблоны сайтов, шаблоны компонентов, шаблоны страниц.

Весь код, который пишется для проекта должен находиться в папке 
```
/local
```

В папке 
```
/bitrix
```

Находится ядро системы, которое изменяется средствами системы обновлений CMS. Никогда не правьте файлы ядра.

### Основной состав шаблона
```
├── /ajax
├── /components
├── /css
│   └── style.css
├── /img
├── /js
│   ├── /vendors
│   ├── partCommon.js
│   └── partMain.js 
├── description.php
├── header.php
├── footer.php
├── styles.css
└── template_styles.css

```

## Структура файла header.php
```php
<?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
/**
 * @global CMain $APPLICATION
 * @global CUser $USER
 */
$curPage = $APPLICATION->GetCurPage(true);
$assets = \Bitrix\Main\Page\Asset::getInstance();
?>
<!DOCTYPE html>
<html lang="<?=LANGUAGE_ID?>">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="x-ua-compatible" content="ie=edge"/>
    <meta name="viewport" content="width=device-width, initial-scale=1"/>

    <title><?$APPLICATION->ShowTitle()?></title>

    <?
    /**
     * CSS
     */
    $assets->addCss(SITE_TEMPLATE_PATH . '/css/style.css');
    /**
     * JS
     */
    \CJSCore::Init(array('jquery'));
    $assets->addJs(SITE_TEMPLATE_PATH . '/js/jquery-3.1.1.min.js');
    $assets->addJs(SITE_TEMPLATE_PATH . '/js/partMain.js');
    
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
    ?>
</head>
<body>
<div id="panel"><?$APPLICATION->ShowPanel();?></div>

<?if (CSite::InDir('/index.php') 
    || CSite::InDir('/catalog/')
    || CSite::InDir('/search/')):?>
    
    ... Разметка ...
<?endif;?>

```

## Структура файла footer.php

```php
    ... Разметка футера с подключением компонентов ...
    <? // можем подключить необходимые файлы, можем использовать условия (см. пример в header.php)?>
    <?require($_SERVER['DOCUMENT_ROOT'].SITE_TEMPLATE_PATH."/inc_popup_forms.php"); ?>
        
    </body>
</html>

```
