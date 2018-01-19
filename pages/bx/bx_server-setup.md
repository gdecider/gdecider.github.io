---
title: Сервер Настройка
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_server-setup.html
summary: false
toc: false
---

## Настройка сервера

### Настройка сжатия

В случае использования связки nginx + apache, т.к. статику (css, js, ...) отдает nginx, то следует отключить сжатие на стороне apache и включить на стороне nginx

```bash
# отключаем модуль сжатия в apache
sudo a2dismod deflate
sudo /etc/init.d/apache2 restart
```
Правим конфиг nginx (```/etc/nginx/```) для активации сжатия

```
server {
    ....
    gzip                on;
    gzip_disable        "msie6";
    gzip_http_version   1.0;
    gzip_min_length     1100;
    gzip_buffers        4 8k;
    gzip_types 
      text/plain 
      text/css 
      application/json 
      application/x-javascript 
      text/xml 
      application/xml 
      application/xml+rss 
      text/javascript 
      application/javascript;
      
    gzip_proxied            expired no-cache no-store private auth;
    gzip_vary               on;
    gzip_comp_level         5; # степень сжатия от 1 до 9
}
```

### Рекомендации по уходу за сервером

По серверу рекомендации следующие:

* 1. Для сервера баз данных разрешить прослушивать порт 3306 только на локальном адресе 127.0.0.1.
* 2. Запретить доступ пользователю root по протоколу ssh (запускать сеанс с минимальными правами).
* 3. Использовать авторизацию ssh по ключам.
* 4. Установить пакет wail2ban   для зашиты от перебора паролей.
* 5. Рекомендую закрыть входящие порты 53,123 если на сервере не крутится DNS и NTP.
* 6. Поставит ограничение на выполнение файлов в каталогах UPLOAD  на веб сервере с маской 0644.
* 7. Еженедельно проводить сканирование сервера на наличие руткитов и другого вредоносного по (можно использовать chkrootkit).
