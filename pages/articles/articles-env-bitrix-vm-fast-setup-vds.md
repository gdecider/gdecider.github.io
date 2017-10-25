---
title: Быстрая настройка окружения для Битрикс на VDS на основе VM Bitrix | Статьи Рабочее окружение
sidebar: articles_sidebar
keywords: bitrix vm vds
permalink: articles-env-bitrix-vm-fast-setup-vds.html
toc: false
folder: articles
---

## Установка Bitrix VM

[курс](https://dev.1c-bitrix.ru/learning/course/?COURSE_ID=37&INDEX=Y)
[форум](https://dev.1c-bitrix.ru/community/forums/forum32/topic98645/)

Поддержка битрикса предоставляет скрипт для быстрой установки окружения на VDS. 
Для его получения и запуска нужно выполнить комманды:

```sh
wget http://repos.1c-bitrix.ru/yum/bitrix-env.sh && chmod +x bitrix-env.sh && ./bitrix-env.sh

Usage: ./bitrix-env.sh [-s] [-p [-H hostname]] [-M mysql_root_password]
-s - Silent or quiet mode. Don't ask any questions.
-p - Create pool after installation of bitrix-env.
-H - Hostname for for pool creation procedure.
-M - Mysql password for root user
```
После выполнения этих команд будет выведена информация о проделаной работе, среди этой информации будет строка **You can find root password at /root/.my.cnf client config file.** забираем пароль для БД и удаляем этот файл.

## Произвольная настройка доменов

* Файл конфигурации хостов apache: ```/etc/httpd/bx/conf/bx_ext_<sitename>.conf```
* Файл конфигурации хостов nginx: ```/etc/nginx/bx/site_enabled/bx_ext_<sitename>.conf```, ```/etc/nginx/bx/site_enabled/bx_ext_ssl_<sitename>.conf``` - это ссылки на файл из директории ```sites_available```

После правки файлов не забываем рестартовать демонов

```
systemctl restart httpd
systemctl restart nginx
```

## Прочие настройки

* поддержка curl

в файл ```/etc/php.d/20-curl.ini``` вносим данные:

```
extension=curl.so
```

* поддержка phar

в файл ```/etc/php.d/20-phar.ini``` вносим данные:

```
extension=phar.so
```
