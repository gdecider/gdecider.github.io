---
title: Статьи 1С Битрикс | Кастомизация функции отправки
keywords: sample homepage
sidebar: articles_sidebar
permalink: articles_bx-send-email-custom-mail.html
summary: false
toc: false
---

## Отправка письма не стандартными средствами

### Задача

Переопределить стандартную функцию отправки почты на свою реализацию.

### Решение
Использовать модуль [local.custommail](https://github.com/gdecider/bx-module-custommail)

### Как это работает
Стандартным способом переопределения процесса отправки почты в Битрикс является переопределение функции [custom_mail](https://dev.1c-bitrix.ru/api_help/main/functions/other/bxmail.php).

**Параметры функции:**

  * to - Получатель.
  * subject - Заголовок письма.
  * message - Тело письма.
  * additional_headers - Дополнительные заголовки.
  * additional_parameters - Дополнительные параметры.

#### PHP

Переопределим функцию custom_mail. Делаем это в init.php, а еще лучше в подключенном к нему файле ```functions.php```

В примере, для отправки ф-ии используем класс [PHPMailer](https://github.com/PHPMailer/PHPMailer). Так же можно использовать, что угодно, лишь бы оно выполняло поставленную задачу. Например есть [swiftmailer](https://swiftmailer.symfony.com/docs/introduction.html)

```php
<?php
function custom_mail($to, $subject, $message, $additional_headers='', $additional_parameters='') {
	require 'PHPMailerAutoload.php';

	$mail = new PHPMailer();

        $mail->CharSet = 'UTF-8';
        $mail->isSMTP();
        $mail->SMTPAuth = true;

        $addresses = array_map('trim', explode(',', $to));

        foreach ($addresses as $address) {
            $mail->addAddress($address);
        }

        $mail->Host = 'тут ваш хост';
        $mail->Username = 'тут ваш логин к почте';
        $mail->Password = 'тут ваш пароль к почте';
        $mail->SMTPSecure = 'тут ваша настройка к почте';
        $mail->Port = 'тут ваш порт к почте';
        $mail->setFrom('тут ваш логин к почте');

        $arRows = preg_split("/((\r?\n)|(\r\n?))/", $additional_headers);
        foreach ($arRows as $header) {
            $mail->addCustomHeader($header);
        }

        $mail->ContentType = $mail::CONTENT_TYPE_MULTIPART_ALTERNATIVE;
        $mail->Subject = $subject;
        $mail->Body = $message;

        if (!$mail->send()) {
            return false;
        }

        return true;
}
?>
```

**Проверка отправки сообщения**

Зайти в Админку Битрикс, далее Настройки -> Инструменты -> SQL-запрос.

```sql
select * from b_event
where event_name like '%MSG_TYPE%'
order by date_insert desc

MSG_TYPE - код типа сообщения
```
