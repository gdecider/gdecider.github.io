---
title: Composer и Битрикс | Статьи CMS "1С Битрикс"
keywords: Composer и Битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-composer-and-bitrix.html
summary: Пример использования Composer и Битрикс, установка модуля из репозитория
toc: false
---

## Как и зачем использовать Composer при создании проекта на Битрикс

Composer - пакетный менеджер уровня приложений для языка программирования PHP, который предоставляет средства по управлению зависимостями в PHP-приложении.

При разработке проекта на Битрикс может возникнуть необходимость написания модуля, получения стороннего модуля, добавления библиотеки для выполнения какого-либо нужного действия в проекте. Процессами установки и определения зависимостей одних модулей от других гораздо проще управлять при помощи composer.

### Установка composer

Инструкции по установке composer на [официальном сайте](https://getcomposer.org/download/)

Установить composer глобально в систему (Linux):

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"
```

После выполнения этих команд composer станет доступен для вызова в консоли командой ```composer```

### Связь Битрикс и Composer

Ряд полезных статей и рассуждений на эту тему:

* [http://thisis-blog.ru/composer-dlya-avtozagruzki-klassov-bitriks/](http://thisis-blog.ru/composer-dlya-avtozagruzki-klassov-bitriks/)
* [http://zhurov.me/blog/bitrix-loves-composer.html](http://zhurov.me/blog/bitrix-loves-composer.html)
* [http://samokhvalov.info/blog/all/ispravlenie-kompozera-dlya-bitriksa/](http://samokhvalov.info/blog/all/ispravlenie-kompozera-dlya-bitriksa/)
* [http://samokhvalov.info/blog/all/composer-installers-1-0-24/](http://samokhvalov.info/blog/all/composer-installers-1-0-24/)

В общем случае, если позволяет хостинг, и сайт вынесен в отдельную папку, например так:

```
/var/www/some-site #site root directory
├── /.git
├── /.idea
├── /public
│   ├── /... папки разделов сайта ...
│   └── /local
├── .gitmodules
├── .gitignore
└── composer.json
```

в такой структуре выносим файл в непубличную часть проекта.

В случае, когда из-за особенностей хостинга или проекта, он не вынесен в отдельную публичную папку, тогда файл настроек composer храним в папке ```local``` так:

```
/var/www/some-site #site root directory
├── /.git
├── /.idea
├── /... папки разделов сайта ...
└── /local
    ├── /... код разработчика и настройки сайта ...
    └── composer.json
├── .gitmodules
└── .gitignore
```

Во всех случаях папка вендоров ```vendor``` добавляется в ```.gitignore```

Пример содержимого файла ```composer.json```

```json
{
    "require": {
        "composer/installers": "~1.0",
    },
}
```

Для установки модулей, перечисленных в файле ```composer.json``` нужно перейти в папку, в которой находится этот файл и выполнить команду:

```bash
composer install
```

После этого composer создаст папку ```vendor```, в нее будут добавлены все нужные модули и создан файл ```autoload.php```

Для того, чтобы Битрикс подключил классы модулей полученных через composer нужно в файл ```init.php``` добавить:

```php
<?
// composer for out public
if (file_exists($_SERVER["DOCUMENT_ROOT"]."/../vendor/autoload.php")) {
    require_once($_SERVER["DOCUMENT_ROOT"] . "/../vendor/autoload.php");
}

// composer for local
if (file_exists($_SERVER["DOCUMENT_ROOT"]."/local/vendor/autoload.php")) {
    require_once($_SERVER["DOCUMENT_ROOT"] . "/local/vendor/autoload.php");
}
?>

### Подготовка модуля Битрикс к установке через composer

Чтобы composer знал о возможности установки модуля Битрикс нужно соблюсти ряд условий:

* Нужно разместить модуль в каком-либо хранилище репозиториев, например github.com.
* В корне модуля разместить файл ```composer.json``` с информацией о модуле и его зависимостях.
  
  Пример файла:
  
  ```json
    {
        "name": "modvendor/modname",
        "description": "module description",
        "type": "bitrix-d7-module",
        "require": {
            "altayalp/ftp-client": "^1.0"
        },
        "authors": [
            {
                "name": "Your Name",
                "email": "your@email.ru"
            }
        ]
    }
  ```
  
### Установка модуля Битрикс через composer

Есть модуль composer, который помогает в установке специфических расширений для CMS и фреймворков, он учитывает особенности архитектуры этих систем и копирует папки модулей не по стандартному пути composer, которым является папка vendor, а в нужную папку в зависимости от системы в которую ставится модуль.

Для битрикс привычными путями для установки модуля являются:

* ```/bitrix/modules/```
* ```/local/modules/```

Модуль-помощник называется composer/installers, почитать о нем можно [тут](http://composer.github.io/installers/), [GitHub](https://github.com/composer/installers)

По умолчанию он устанавливает модули Битрикс в папку ```/bitrix/modules/```, что бы это заработало в файле композера нужно разместить такие инструкции:

```json
{
    "name": "yourcompany/projectname",
    "type": "project",
    "authors": [
        {
            "name": "Your Name",
            "email": "your@email.ru"
        }
    ],
    "require": {
        "composer/installers": "~1.0",
        "your/modulename": "dev-master"
    },
    "config": {
    	"github-oauth": {
			"github.com": "your access token"
		}
    },
    "repositories": [
        {
            "type": "vcs",
            "url":  "https://github.com/yourlogin/your.modulename.git"
        }        
    ]
}

```

Для изменения пути установки и прочих параметров (о которых можно почитать тут), нужно добавить секцию "extra", пример:

```json
{
    "name": "yourcompany/projectname",
    "type": "project",
    "authors": [
        {
            "name": "Your Name",
            "email": "your@email.ru"
        }
    ],
    "extra": {
        "installer-paths": {
            "public/local/modules/{$vendor}.{$name}/": ["type:bitrix-d7-module"]
        }
    },
    "require": {
        "composer/installers": "~1.0",
        "your/modulename": "dev-master"
    },
    "config": {
    	"github-oauth": {
			"github.com": "your access token"
		}
    },
    "repositories": [
        {
            "type": "vcs",
            "url":  "https://github.com/yourlogin/your.modulename.git"
        }        
    ]
}

```

### Получение токена GitHub для Composer

При попытке получения модулей из репозитория, размещенного на GitHub может появиться ошибка: **Could not fetch ######, please create a GitHub OAuth token to go over the API rate limit**

Для исправления этой ошибки в файл ```composer.json``` нужно добавить секцию:

```json
"config": {
    "github-oauth": {
        "github.com": "your access token"
    }
},
```

Получить токен нужно в Вашем аккаунте на GitHub в разделе настроек [https://github.com/settings/tokens](https://github.com/settings/tokens)
