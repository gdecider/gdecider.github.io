---
title: Статьи 1С Битрикс | Отправка письма
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx_send_email.html
summary: false
toc: false
---

## Отправка письма

### Задача

Отправить письмо при помощи API Битрикс

### Решение 1. С использованием административной патели Битрикс.

#### Битрикс

  1) Создайте тип почтового события. В нашем примере "TMP_MAIL_EVENT".

  2) Создайте почтовый шаблон для созданного события.

#### PHP обработчик

```php
<?php
$request = \Bitrix\Main\Context::getCurrent()->getRequest();

// проверим наличие данных
$field = htmlspecialcharsEx($request->getPost('field'));

// сохраним нужные для отправки поля в массив
$arEmailData = array(
    'FIELD' => $field,
);

// шлем почту
CEvent::Send("TMP_MAIL_EVENT", SITE_ID, $arEmailData);
?>
```
