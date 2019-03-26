---
title: Сервер Настройка редиректов
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_server-redirects.html
summary: false
toc: false
---

## Настройка редиректов

### Сервисы

* [https://bertal.ru/](Тестирование редиректов)

### Nginx

[http://stackoverflow.com/questions/7947030/nginx-no-www-to-www-and-www-to-no-www](http://stackoverflow.com/questions/7947030/nginx-no-www-to-www-and-www-to-no-www)

#### www -> no www

```
server {
    server_name  www.domain.com;
    rewrite ^(.*) http://domain.com$1 permanent;
}

server {
    server_name  domain.com;
    #The rest of your configuration goes here#
}
```

1) создать файл include conf/disable/log.conf;

```
access_log      off;
log_not_found   off;
```

2) сами редиректы

```
server {
    listen              80;
    server_name www.tandoors.ru tandoors.ru;
    include conf/disable/log.conf;
    return 301 https://tandoor.ru$request_uri ;
}

server {
    listen              80;
    server_name www.tandoor.ru tandoor.ru;
    include conf/disable/log.conf;
    return 301 https://tandoor.ru$request_uri ;
}

server {
    listen              443 ssl;
    server_name www.tandoors.ru tandoors.ru;
    include conf/disable/log.conf;
    return 301 https://tandoor.ru$request_uri ;
}

server {
    listen              443 ssl;
    server_name www.tandoor.ru;
    include conf/disable/log.conf;
    return 301 https://tandoor.ru$request_uri ;
}

server {
    # Включение HTTPS и HTTP/2
    listen	443 ssl http2;
    server_name tandoor.ru;
    # ... остальная часть настроек ...
}
```

### Apache

[Как работает mode_rewrite и как правильно писать и читать правила RewriteRule и RewriteCond](https://andew.ru/ru/pages/page/apache-mod-rewrite)

[Синтаксис htaccess, полное руководство](https://www.imbf.org/vebmaster/htaccess-sintaksis-polnoe-rukovodstvo.html)

[Настройка файла htaccess](https://losst.ru/nastrojka-fajla-htaccess)

301 редирект (переадресация) через .htaccess – на все случаи жизни

[http://web-optimizator.com/301-redirekt-htaccess/](http://web-optimizator.com/301-redirekt-htaccess/)

Советы

    Располагайте переадресации страниц в файле от частных к более глобальным (сверху вниз). Например: простая переадресация двух страниц стоит выше, чем глобальное правило редиректов с www на без www.
    Избегайте двух, трех и т.д. последовательных редиректов. Правила должны быть настроены так, что при возникновении редиректа он должен перенаправлять пользователя (робота) только один (!) раз. Каждое лишнее переадресация – это секунды драгоценного времени отдачи страницы, это нагрузка на сервер, это нечеткие команды для поисковых роботов.
    Не забывайте, что многие браузеры кешируют (запоминают редиректы), поэтому проверять переадресации лучше на сайте — http://www.bertal.ru.

Помним что нужно заключать редиректы в теги и включать работу модуля:

```
<IfModule mod_rewrite.c>
  Options +FollowSymLinks
  RewriteEngine On

  # тут пишем правила перенаправления

</IfModule>
```

#### Правила переадресаций

1. 301 редирект с одной страницы на другую

* Самый простой вариант

```
Redirect 301 /test-1/ http://site.ru/test-2/
```

* Более подробный

```
RewriteCond %{REQUEST_URI} ^/test/$
RewriteRule ^.*$ http://site.ru/new-test/? [R=301,L]
```

2. 301 редирект с www на без www (главное зеркало – домен без www)

```
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)$ http://%1/$1 [L,R=301]
```

3. 301 редирект с без www на www (главное зеркало – домен с www)

```
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)$ http://www.%1/$1 [L,R=301]
```

4. 301 редирект со страниц со слешем на без слеша (весь сайт)

```
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} ![^\/]$
RewriteRule ^(.*)\/$ /$1 [R=301,L]
```

5. 301 редирект со страниц без слеша на слеш (часто в CMS системах устанавливается автоматически)

```
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteRule ^(.*[^\/])$ /$1/ [R=301,L]
```

6. Один (а не два последовательных!) 301 редирект на без www и с слешем на конце адреса страницы

```
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)$ http://%1/$1/ [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} ![^\/]$
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)$ http://%1/$1 [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)$ http://%1/$1/ [L,R=301]
```

7. Один (а не два последовательных!) 301 редирект на c www и со слешем на конце адреса страницы

```
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)$ http://www.%1/$1/ [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)$ http://www.%1/$1/ [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} ![^\/]$
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)$ http://www.%1/$1 [L,R=301]
```

8. Один (а не два последовательных!) 301 редирект на c www и без слеша на конце адреса страницы

```
RewriteCond %{REQUEST_URI} ^\/$
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)$ http://www.%1/$1 [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} \/$
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)\/$ http://www.%1/$1 [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)$ http://www.%1/$1 [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} \/$
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)\/$ http://www.%1/$1 [L,R=301]

```

9. Один (а не два последовательных!) 301 редирект на без www и без слеша на конце адреса страницы

```
RewriteCond %{REQUEST_URI} ^\/$
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)$ http://%1/$1 [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} \/$ 
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)\/$ http://%1/$1 [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} !\/$
RewriteCond %{HTTP_HOST} ^www\.(.*)$
RewriteRule ^(.*)$ http://%1/$1 [L,R=301]
 
RewriteCond %{REQUEST_URI} !\?
RewriteCond %{REQUEST_URI} !\&
RewriteCond %{REQUEST_URI} !\=
RewriteCond %{REQUEST_URI} !\.
RewriteCond %{REQUEST_URI} \/$
RewriteCond %{HTTP_HOST} ^([^www].*)$
RewriteRule ^(.*)\/$ http://%1/$1 [L,R=301]
```

10. 301 редирект только адреса site.ru/index.php (без GET параметров) на основное зеркало site.ru

```
RewriteCond %{REQUEST_URI} /index.php
RewriteCond %{QUERY_STRING} ^\z
RewriteRule ^(.*)$ http://site.ru/? [R=301,L]
```

11. 301 редирект всех адресов с index.php и GET параметрами на страницы только с GET параметрами (вырезать в url index.php)

Пример: типа site.ru/index.php?n=1 на site.ru/?n=1

```
RewriteCond %{REQUEST_URI} /index.php
RewriteRule ^(.*)$ http://site.ru/ [R=301,L]
```

12. 301 редирект для index.php в Joomla (массовая склейка)

```
RewriteCond %{THE_REQUEST} ^[A-Z]{3,9}\ /index\.php\ HTTP/
RewriteRule ^index\.php$ http://dikom-neva.ru/$1 [R=301,L]
```

13. 301 редирект url с GET параметрами (динамический URL) на статический

1 вариант (простой адрес с GET параметром)

```
RewriteCond %{QUERY_STRING} ^id=229
RewriteRule ^.*$ /supermodel/? [R=301,L]
```

2 вариант (со страницы и GET параметром)

```
RewriteCond %{REQUEST_URI} /test/
RewriteCond %{QUERY_STRING} ^id=229
RewriteRule ^.*$ /supermodel/? [R=301,L]
```

14. Все страницы одного домена на главную страницу другого домена

```
RewriteCond %{REQUEST_URI} (.*)
RewriteRule ^(.*)$ http://site.ru/ [L,R=301]
```

15. Каждая страница одного домена на такой же адрес другого url

```
RewriteCond %{REQUEST_URI} (.*)
RewriteRule ^(.*)$ http://site.ru/$1 [L,R=301]
```

16. Редирект с протокола http на https.

```
RewriteCond %{HTTPS} !=on
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]
```

Если возникает циклический редирект, то воспользуйтесь этим вариантом:

```
RewriteCond %{HTTPS} off
RewriteCond %{HTTP:X-Forwarded-Proto} !https
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

17. Редирект с протокола https на http.

Обратный редирект, если перестали использовать SSL сертификат.

```
RewriteCond %{HTTPS} =on
RewriteRule ^(.*)$ http://%{HTTP_HOST}/$1 [R=301,L]
```

18. Удалить повторяющиеся слеши

```
RewriteCond %{THE_REQUEST} //
RewriteRule (.*) /$0 [R=301,L]
```
