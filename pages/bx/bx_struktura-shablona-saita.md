---
title: Структура шаблона сайта
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_struktura-shablona-saita.html
summary: false
toc: false
---
## Структура шаблона сайта

### Путь к шаблону сайта

Основной шаблон сайта должен находиться в папке
```
/local/templates/<имя основного шаблона сайта>
```

В качесте имени шаблона рекомендуем использовать "mainTpl" для подержки единообразия именования в разрезе всех проектов компании.

### Основной состав шаблона
```
└── mainTpl
    ├── /ajax
    ├── /components
    ├── /css
    ├── /fonts
    │   └── /font-family-name
    ├── /favicon
    ├── /img
    ├── /include
    ├── /page_templates
    │   └── .content.php
    ├── /sass
    │   ├── /parts
    │   │   ├── _mixins.scss
    │   │   ├── _fonts.scss
    │   │   ├── _common.scss
    │   │   ├── _page-main.scss
    │   │   └── _page-404.scss
    │   └── style.scss
    ├── /.sass-cache
    ├── /js
    │   ├── /vendors
    │   ├── partCommon.js
    │   └── partMain.js
    ├── header.php
    ├── footer.php
    ├── template_styles.php
    ├── styles.css
    ├── .styles.php
    └── description.php

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
    
    <link rel="shortcut icon" href="<?=SITE_TEMPLATE_PATH?>/favicon/favicon.png">
    
    <link rel="apple-touch-icon" sizes="57x57" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-57x57.png">
    <link rel="apple-touch-icon" sizes="60x60" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-60x60.png">
    <link rel="apple-touch-icon" sizes="72x72" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-72x72.png">
    <link rel="apple-touch-icon" sizes="76x76" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-76x76.png">
    <link rel="apple-touch-icon" sizes="114x114" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-114x114.png">
    <link rel="apple-touch-icon" sizes="120x120" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-120x120.png">
    <link rel="apple-touch-icon" sizes="144x144" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-144x144.png">
    <link rel="apple-touch-icon" sizes="152x152" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-152x152.png">
    <link rel="apple-touch-icon" sizes="180x180" href="<?=SITE_TEMPLATE_PATH?>/favicon/apple-icon-180x180.png">
    <link rel="icon" type="image/png" sizes="192x192"  href="<?=SITE_TEMPLATE_PATH?>/favicon/android-icon-192x192.png">
    <link rel="icon" type="image/png" sizes="32x32" href="<?=SITE_TEMPLATE_PATH?>/favicon/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="96x96" href="<?=SITE_TEMPLATE_PATH?>/favicon/favicon-96x96.png">
    <link rel="icon" type="image/png" sizes="16x16" href="<?=SITE_TEMPLATE_PATH?>/favicon/favicon-16x16.png">
    <link rel="manifest" href="<?=SITE_TEMPLATE_PATH?>/favicon/manifest.json">
    
    <meta name="msapplication-TileColor" content="#ffffff">
    <meta name="msapplication-TileImage" content="<?=SITE_TEMPLATE_PATH?>/favicon/ms-icon-144x144.png">
    <meta name="theme-color" content="#ffffff">

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
