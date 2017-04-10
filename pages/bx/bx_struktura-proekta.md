---
title: Структура проекта
keywords: sample homepage
sidebar: bx_sidebar
permalink: bx_struktura-proekta.html
summary: false
toc: true
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
│   │   │   │   ├── env_vars.php
│   │   │   │   ├── env_vars.example.php
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

## /.git

Основной репозиторий проекта, в нем содержится все, что относится к разработке проекта.

## /.idea

Файлы относящиеся к IDE. Название папки зависит от используемой вами IDE, если отличается от указанного, то нужно включить вашу папку в исключения git.

## /public

Корневая директория сайта, все файлы, доступные из сети находятся в ней.

## /bitrix

Ядро систему 1С Битрикс, изменять файлы в этой директории категорически запрещается, исключением являются файлы, поддержка которых не реализована в папке локал, например файлы настроек БД.

## /bitrix/.git

Репозиторий подмодуля ядра системы.

Для удобства отслеживания изменений в ядре системы оно должно помещаться в отдельный репозиторий, который подключается к основному репозиторию как подмодуль гит.

## /bitrix/.gitignore

Файл исключений для репозитория ядра системы.

## /bitrix/php_interface/dbconn.php

Файл конфигурации для старого ядра Битрикс. В целях повышения безопасности этот файл включен в исключения git, для настройки системы при переносе из репозитория присутствует пример файла настроек dbconn.example.php

## /bitrix/php_interface/dbconn.example.php

Пример файла настроек.

## /bitrix/.settings.php

Файл конфигурации для нового ядра Битрикс. В целях повышения безопасности этот файл включен в исключения git, для настройки системы при переносе из репозитория присутствует пример файла настроек .settings.example.php

## /public/bitrix/.settings.example.php

Пример файла настроек.

## /upload

Папка с файлами, загруженными пользователями или обменом с 1С. Добавлена в исключения git.

## /local

Основная папка для размещения кода проекта.

## /local/activities


## /local/modules

## /local/modules/local.common

## /local/components

## /local/php_interface

## /local/php_interface/include

## /local/php_interface/include/constants.php

## /local/php_interface/include/handlers.php

## /local/php_interface/include/env_vars.php

Константы специфичные для окружения. Файл добавлен в исключения git.

Пример: константа, отвечающая за определения типа окружения IS_PRODUCTION

## /local/php_interface/include/env_vars.example.php

Пример файла констант окружения.

## /local/php_interface/include/site_closed.php

## /local/php_interface/init.php

## /local/php_interface/this_site_support.php

## /local/gadgets

## /local/logs

## /local/verstka

## /local/verstka/.git

## /local/verstka/.gitignore

## /local/templates

## /local/templates/mainTpl

## /local/templates/mainTpl/ajax

## /local/templates/mainTpl/components

## /local/templates/mainTpl/css

## /local/templates/mainTpl/fonts

## /local/templates/mainTpl/fonts/font-family-name

## /local/templates/mainTpl/favicon

## /local/templates/mainTpl/img

## /local/templates/mainTpl/include

## /local/templates/mainTpl/page_templates

## /local/templates/mainTpl/page_templates/.content.php

## /local/templates/mainTpl/sass

## /local/templates/mainTpl/sass/parts

## /local/templates/mainTpl/sass/parts_mixins.scss

## /local/templates/mainTpl/sass/parts_fonts.scss

## /local/templates/mainTpl/sass/parts_common.scss

## /local/templates/mainTpl/sass/parts_page-main.scss

## /local/templates/mainTpl/sass/parts_page-404.scss

## /local/templates/mainTpl/sass/style.scss

## /local/templates/mainTpl/.sass-cache

## /local/templates/mainTpl/js

## /local/templates/mainTpl/js/vendors

## /local/templates/mainTpl/js/partCommon.js

## /local/templates/mainTpl/js/partMain.js

## /local/templates/mainTpl/header.php

## /local/templates/mainTpl/footer.php

## /local/templates/mainTpl/template_styles.php

## /local/templates/mainTpl/styles.css

## /local/templates/mainTpl/.styles.php

## /local/templates/mainTpl/description.php

## index.php

## urlrewrite.php

## .access.php

## .htaccess

## .menu-name.menu.php

## .menu-name.menu_ext.php

## robots.txt

## robots.example.txt

## 404.php

## .gitignore

```
### общие исключения
# IDE
.idea/

# dev backups
*bak20*
*bak-20*

# cache
.sass-cache/

## если гит выше корня сайта
/public/upload/
/public/.Trash*/
/public/yml-catalogs/*.yml
/public/robots.txt
/public/sitemap*.xml
/public/local/vendor/

## если гит в корне сайта
/upload/
/.Trash*/
/yml-catalogs/*.yml
/robots.txt
/sitemap*.xml
/local/vendor/


### Битрикс
## если гит в корне сайта

# configs
/bitrix/php_interface/dbconn.php
/bitrix/.settings.php

# backups
/bitrix/backup/*
!/bitrix/backup/index.php

# logs
/bitrix/*.log
/bitrix/modules/updater.log
/bitrix/modules/updater_partner.log

# cache 
/bitrix/cache/*
/bitrix/managed_cache/*
/bitrix/stack_cache/*
/bitrix/html_pages/

## если гит выше корня сайта
# configs
/public/bitrix/php_interface/dbconn.php
/public/bitrix/.settings.php

# backups
/public/bitrix/backup/*
!/public/bitrix/backup/index.php

# logs
/public/bitrix/*.log
/public/bitrix/modules/updater.log
/public/bitrix/modules/updater_partner.log

# cache 
/public/bitrix/cache/*
/public/bitrix/managed_cache/*
/public/bitrix/stack_cache/*
/public/bitrix/html_pages/

### исключения для конкретного проекта
/bitrix/modules/iarga.exchange/
!/bitrix/modules/iarga.exchange/install/
!/bitrix/modules/iarga.exchange/lang/
```

## .gitmodules

```
[submodule "bitrix"]
	path = bitrix
	url = <ссылка на репозиторий подмодуля>
```