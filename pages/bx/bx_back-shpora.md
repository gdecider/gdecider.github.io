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