---
title: Статьи 1С Битрикс | Протокол HTTPS
keywords: битрикс HTTPS, определение протокола, подстановка протокола при переадресации
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-https.html
summary: false
toc: false
---

## Как Битрикс понимает какой протокол использовать

Понять, как БУС определяет, какой протокол, HTTP или HTTPS, использовать на сайте, удобнее всего по коду ф-ии ```LocalRedirect```, 
которая расположена в файле ```bitrix/modules/main/tools.php```

Код определения протокола таков: 

```php
<?
$protocol = (CMain::IsHTTPS() ? "https" : "http");
```
Сам метод является оберткой над

```php
<?
\Bitrix\Main\Context::getCurrent()->getRequest()->isHttps();
```

Который, в свою очередь, смотрит на 3 параметра системы:

1) Серверная переменная **SERVER_PORT**
   ```php
   <?
   $this->server->get("SERVER_PORT") == 443
   ```
2) Серверная переменная **HTTPS**
   ```php
   <?
   $https = $this->server->get("HTTPS");
   if($https <> '' && strtolower($https) <> "off") {
			return true;
   }
   ```
3) Конфигурационная переменная **https_request** в файле настроек ```.settings.php```
   ```php
   <?
   Config\Configuration::getValue("https_request") === true
   ```
   
Из выше описаного видно, какие параметры можно настроить в БУС, что бы он использовал нужный вам протокол на сайте HTTP или HTTPS.
