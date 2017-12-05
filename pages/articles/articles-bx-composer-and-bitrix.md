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
php composer-setup.php --install-dir=bin --filename=composer
php -r "unlink('composer-setup.php');"
```

После выполнения этих команд composer станет доступен для вызова в консоли командой ```composer```

### Связь Битрикс и Composer

Ряд полезных статей и рассуждений на эту тему:

* [http://thisis-blog.ru/composer-dlya-avtozagruzki-klassov-bitriks/](http://thisis-blog.ru/composer-dlya-avtozagruzki-klassov-bitriks/)
* [http://zhurov.me/blog/bitrix-loves-composer.html](http://zhurov.me/blog/bitrix-loves-composer.html)
* [http://samokhvalov.info/blog/all/ispravlenie-kompozera-dlya-bitriksa/](http://samokhvalov.info/blog/all/ispravlenie-kompozera-dlya-bitriksa/)
* [http://samokhvalov.info/blog/all/composer-installers-1-0-24/](http://samokhvalov.info/blog/all/composer-installers-1-0-24/)
* [http://epages.su/blog/migratsii-bazy-dannykh-v-1s-bitirks-proektakh-s-ispolzovaniem-phinx.html](http://epages.su/blog/migratsii-bazy-dannykh-v-1s-bitirks-proektakh-s-ispolzovaniem-phinx.html)

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

Во всех случаях папка вендоров ```vendors``` добавляется в ```.gitignore```


