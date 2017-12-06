---
title: Настройка почты в BitrixVM | Статьи Рабочее окружение
sidebar: articles_sidebar
keywords: bitrix vm email
permalink: articles-env-bitrix-vm-mail-config.html
toc: false
folder: articles
---

### Настройка

В BitrixVM в качестве почтовика используется **msmtp**

Файл настроек расположен тут: ```/home/bitrix/.msmtprc```

Пример настройки почты для Яндекса:

```
# smtp account configuration for default
account default
logfile /home/bitrix/msmtp.log
host smtp.yandex.ru
port 587
from somemail@yandex.ru
keepbcc on
auth on
user someusername
password someuserpass
tls on
tls_starttls on
tls_certcheck off
```

### Проверка отправки почты

В командной строке выполнить:

```
php -r "mail('test@email.com', 'Test', 'Test');"
```
