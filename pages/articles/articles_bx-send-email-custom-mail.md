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

Стандартным способом переопределения процесса отправки почты в Битрикс является переопределение функции custom_mail.

**Параметры функции:**

  * to - Получатель.
  * subject - Заголовок письма.
  * message - Тело письма.
  * additional_headers - Дополнительные заголовки.
  * additional_parameters - Дополнительные параметры.

#### PHP

Переопределим функцию custom_mail. Делаем это в init.php, а еще лучше в подключенном к нему файле ```functions.php```

Для отправки ф-ии используем класс [PHPMailer](https://github.com/PHPMailer/PHPMailer)

```php
<?php
function custom_mail($to, $subject, $message, $additional_headers='', $additional_parameters='') {
	require 'PHPMailerAutoload.php';

	$mail = new PHPMailer;

	$mail->isSMTP();                                      // Set mailer to use SMTP
	$mail->Host = 'smtp1.example.com;smtp2.example.com';  // Specify main and backup SMTP servers
	$mail->SMTPAuth = true;                               // Enable SMTP authentication
	$mail->Username = 'user@example.com';                 // SMTP username
	$mail->Password = 'secret';                           // SMTP password
	$mail->SMTPSecure = 'tls';                            // Enable TLS encryption, `ssl` also accepted
	$mail->Port = 587;                                    // TCP port to connect to

	$mail->setFrom('from@example.com', 'Mailer');
	$mail->addAddress('joe@example.net', 'Joe User');     // Add a recipient
	$mail->addAddress('ellen@example.com');               // Name is optional
	$mail->addReplyTo('info@example.com', 'Information');
	$mail->addCC('cc@example.com');
	$mail->addBCC('bcc@example.com');

	$mail->addAttachment('/var/tmp/file.tar.gz');         // Add attachments
	$mail->addAttachment('/tmp/image.jpg', 'new.jpg');    // Optional name
	$mail->isHTML(true);                                  // Set email format to HTML

	$mail->Subject = 'Here is the subject';
	$mail->Body    = 'This is the HTML message body <b>in bold!</b>';
	$mail->AltBody = 'This is the body in plain text for non-HTML mail clients';

	if(!$mail->send()) {
	    echo 'Message could not be sent.';
	    echo 'Mailer Error: ' . $mail->ErrorInfo;
	} else {
	    echo 'Message has been sent';
	}
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