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

