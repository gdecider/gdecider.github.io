---
title: Статьи 1С Битрикс | Описание процесса обмена с 1С
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-1c-exch-base.html
summary: false
toc: true
---

## Описание процесса обмена с 1С

### Инструменты

* [https://github.com/parserme/bitrexchange](https://github.com/parserme/bitrexchange)

* [https://github.com/gdecider/bxc-1c.exchange.checker](https://github.com/gdecider/bxc-1c.exchange.checker)

### Статьи

[Процесс обмена Битрикса и 1С](https://mrcappuccino.ru/blog/post/1c-exchange)

### Ручной пошаговый вызов

[https://mrcappuccino.ru/blog/post/delete-1c-bx-import-php-urgently](https://mrcappuccino.ru/blog/post/delete-1c-bx-import-php-urgently)

* кинуть файлы обмена в /upload/1c_exchange/
* загрузка товаров
  ```/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=ИМЯ_ФАЙЛА{import.xml, offers.xml и т.д.}```
* выгрузка заказов с сайта
  ```/bitrix/admin/1c_exchange.php?type=sale&mode=query```
* импорт заказа из 1с
  ```/bitrix/admin/1c_exchange.php?type=sale&mode=import&sessid=SESSID&filename=FILENAME```

### Шаги

[источник](https://dev.1c-bitrix.ru/support/forum/forum6/topic104459/)

Вот алгоритм обмена данными между сервером 1с и битрикс, в случаи если у Вас нет установленной 1с и требуется эмулировать загрузку:

Шаг 1:
Заходим по адресу test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=checkauth
Сервер отвечает:
PHPSESSID
123456789
PHPSESSID=123456789
09.01.2018T10:10:10

Шаг 2:
Заходим по адресу: test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=init&version=2&PHPSESSID=123456789
Сервер отвечает:
zip=yes
file_limit=10000
PHPSESSID=123456789
version=2

Шаг 3:
Заходим по адресу: test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=file&filename=catalog.zip&PHPSESSID=123456789
Сервер отвечает:
success

ТУТ заходим в папку /upload/1c_exchange/ и кладем туда файл с catalog.zip (в нем уже должен быть import.xml и т.п.)

Третий шаг закончен. Приступаем к шагу 4:
Заходим по адресу: test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=catalog.zip&PHPSESSID=123456789
Он ответит progress и описание. Выполняем этот запрос пока он не скажет success или failed


Шаг 5:

http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=catalog.zip&PHPSESSID=123456789

Сервер отвечает:
progress Распаковка архива завершена.

Шаг 6:

http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=import.xml&PHPSESSID=123456789


Ответ сервера:
progress Временные таблицы удалены.

Шаг 7
Ещё раз обращаемся по той же строчке: (то есть по строчке http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=import.xml&PHPSESSID=123456789)

Ответ сервера:
progress Временные таблицы созданы.


Шаг 8
Ещё раз обращаемся по той же строчке: (то есть по строчке http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=import.xml&PHPSESSID=123456789)

Сервер отвечает
progress Файл импорта прочитан.


Шаг 9
Ещё раз обращаемся по той же строчке: (то есть по строчке http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=import.xml&PHPSESSID=123456789)


Сервер отвечает:
progress Временные таблицы проиндексированы.


Шаг 10
Ещё раз обращаемся по той же строчке: (то есть по строчке http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=import.xml&PHPSESSID=123456789)


Сервер отвечает:
progress Метаданные импортированы успешно.


Шаг 11
Ещё раз обращаемся по той же строчке: (то есть по строчке http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=import.xml&PHPSESSID=123456789)


Сервер отвечает:
progress Группы импортированы.


Шаг 12
Ещё раз обращаемся по той же строчке: (то есть по строчке http://test.ru/bitrix/admin/1c_exchange.php?type=catalog&mode=import&filename=import.xml&PHPSESSID=123456789)


Сервер отвечает:
progress Деактивация/удаление групп завершено.

и так далее пока сервер не ответит
success Импорт успешно завершен.

Благодарю пользователя форума Битрикс - Андрея Николаева, за помощь и разъяснения по данному вопросу на этапе практических тестов.
