---
title: Получение и настройка бесплатного SSL сертификата Let's Encrypt для сайта с помощью CertBot
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_server-ssl-lets-encrypt.html
summary: false
toc: true
---

## Рекомендации от Битрикс по переводу сайта на https

[Настройка HTTPS-соединения](https://dev.1c-bitrix.ru/learning/course/index.php?COURSE_ID=35&LESSON_ID=3261&LESSON_PATH=3906.4493.4829.3261)

## Получение и настройка бесплатного SSL сертификата Let's Encrypt для сайта с помощью CertBot
- Для успешного получения сертификата домен должен быть делигирован и доступен по адресу на машине, где будет запускаться генерация сертификата.
- Сертификат действует в течении 90 дней, поэтому необходимо настроить его автоматическое обновление.
- Предполагается, что корневая директория веб-сервера находится в /var/www

### Установка и настройка CertBot
Клиент CertBot можно найти в репозитории некоторых дистрибутивов систем, но скорее всего там он будет весьма неактуальной версии, поэтому устанавливать лучше вручную.

1. Скачиваем скрипт
    ```bash
    $ cd ~
    $ wget https://dl.eff.org/certbot-auto
    $ chmod a+x certbot-auto
    $ sudo mv certbot-auto /usr/local/bin
    $ sudo chown root:root /usr/local/bin/certbot-auto
    $ sudo ln -s /usr/local/bin/certbot-auto /usr/local/bin/certbot
    ```
    Скрипт является обёрткой над CertBot, который всегда пытается получить самую актуальную версию и принимает те же параметры, что и сам CertBot. Доступен при выполнении команды `certbot` из любого места.
2. Устанавливаем зависимости
    ```bash
    $ sudo certbot --noninteractive --os-packages-only
    ```
3. Чтобы избавить себя и других от запоминания параметров команды, необходимо настроить файл конфиругаций в `/etc/letsencrypt/cli.ini`

    Создается файл следующими командами, после чего можно его отредактировать.
     ```bash
     $ sudo mkdir -p /etc/letsencrypt
     $ sudo bash -c 'cat > /etc/letsencrypt/cli.ini' <<EOF
       rsa-key-size = 4096
       # Необходимо указать почту куда будут приходить уведомления о необходимости
       # продлить сертификат, если что-то пошло не так и автообновление не сработало.
       email = admin@example.com
       text = True
       non-interactive = True
       agree-tos = True
       authenticator = webroot
       webroot-path = /var/www/letsencrypt
       EOF
     $ sudo nano /etc/letsencrypt/cli.ini
     ```
4. Создаем ежедневную Cron задачу на обновление сертификатов
    ```bash
    $ sudo touch /etc/cron.daily/certbot-renew
    $ sudo chmod a+x /etc/cron.daily/certbot-renew
    $ sudo bash -c 'cat > /etc/cron.daily/certbot-renew' <<EOF
    #!/bin/bash
    #
    # Обновление ранее полученных сертификатов Let's encrypt
    #
    export HOME="/root"
    export PATH="\${PATH}:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
    certbot --no-self-upgrade renew
    if service --status-all | grep -Fq 'apache2'; then
       service apache2 reload
    fi
    if systemctl list-units --type service | grep -Fq 'httpd'; then
       service httpd reload
    fi
    if service --status-all | grep -Fq 'nginx'; then
       service nginx reload
    fi
    EOF
    ```
5. Настройка webroot для валидации домена при получении сертификата.

    Для этого в зависимости от используемого веб-сервера необходимо дать доступ к следующему адресу http://домен-сайта/.well-known/acme-challenge/ok.html
    ```bash
    $ sudo mkdir -p /var/www/letsencrypt/.well-known/acme-challenge
    $ sudo echo OK > /var/www/letsencrypt/.well-known/acme-challenge/ok.html
    $ sudo chown apache:apache -R /var/www/letsencrypt
    ```
    Ниже будут приведены настройки для наиболее часто используемого веб-сервера Nginx:
    ```bash
    $ sudo mkdir -p /etc/nginx/conf/locations
    $ sudo bash -c 'cat > /etc/nginx/conf/locations/letsencrypt.conf' <<EOF
    # Разрешает домену пройти проверку на выдачу SSL сертификата и дальнейшее его продление
    location ^~ /.well-known/acme-challenge/ {
        default_type "text/plain";
        root         /var/www/letsencrypt;
    }
    # Скрывает /acme-challenge и возвращает 404 на все запросы.
    location = /.well-known/acme-challenge/ {
        return 404;
    }
    EOF
    ```
    Далее открываем конфиг домена, для которого настраиваются сертификаты и в блоке `server` на 80 порту находим первый `location` и перед ним добавляем следующее: `include conf/locations/letsencrypt.conf;`
    - Проверяем, что всё сделали правильно командой `sudo nginx -t` и нет никаких ошибок.
    - Затем перезапускаем nginx `sudo service nginx reload`
    - Открываем сайт по адресу http://домен-сайта/.well-known/acme-challenge/ok.html и видим сообщение ОК, если не видим, значит что-то сделано не так, вероятно код `include conf/locations/letsencrypt.conf;` размещен не в нужном месте.
    - После чего уже можно проверить работу получения сертификата, запустив команду в режиме для тестов:

    ```bash
    $ rm -f /var/www/letsencrypt/.well-known/acme-challenge/ok.html
    $ sudo certbot certonly --dry-run -d мой-домен.com
    ```
    В конце программа должна отчитаться об успешной работе: `The dry run was successful.`
    
    После чего уже можно получать настоящий сертификат.

6. Получение сертификата
    - Сертификат можно получить для домена и всех его реальных рабочих поддоменов следующей командой:
    ```bash
    $ sudo certbot certonly -d мой-домен.com -d www.мой-домен.com -d shop.мой-домен.com
    ```
    - Для каждого домена второго уровня желательно запускать новую команду.
    - Проверить сгенерированные сертификаты можно сделующей командой: `sudo find /etc/letsencrypt/live/ -type l`

7. Добавление/Изменение поддоменов в уже сгенерированный сертификат.
    - Добавить можно с помощью указания специального параметра `--expand`
    - Комада работает в режиме "перезапись", поэтому указывать необходимо ВСЕ доменные имена, которые были добавлены в сертификат ранее.
    ```bash
    $ sudo certbot certonly --expand -d мой-домен.com -d www.мой-домен.com -d shop.мой-домен.com
    ```

8. Генерируем dhparam.pem <https://weakdh.org/sysadmin.html>
```bash
sudo mkdir -p /etc/ssl/private
sudo openssl dhparam -out /etc/ssl/private/dhparams_2048.pem 2048
```
### Настройка веб-сервера Nginx на работу с SSL сертификатами

#### Делаем общие настройки для всех сайтов, работающих с сертификатами
```bash
$ sudo mkdir -p /etc/nginx/conf/enable
$ sudo bash -c 'cat > /etc/nginx/conf/enable/ssl.conf' <<EOF
# SSL Общие настройки
ssl_session_cache	shared:SSL:10m;
ssl_session_timeout     10m;
ssl_prefer_server_ciphers   on;
ssl_stapling    on;
resolver        8.8.8.8;
ssl_protocols   TLSv1 TLSv1.1 TLSv1.2;
# Список максимально защищенных шифров с https://cipherli.st/. Не поддерживаются некоторые клиенты: IF6/XP, IE8/XP, Java 6u45, Java 7u25, OpenSSL 0.9.8y
ssl_ciphers     "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
# Менее защищенные шифры. Не поддерживается: IF6/XP, Java 6u45
#ssl_ciphers    "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH:ECDHE-RSA-AES128-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA128:DHE-RS$
# Use unique [Diffie-Hellman Group](https://weakdh.org/sysadmin.html): `openssl dhparam -out dhparams.pem 2048`
ssl_dhparam     /etc/ssl/private/dhparams_2048.pem;
add_header	Strict-Transport-Security 'max-age=604800';
EOF
```

#### Подключаем сгенерированный сертификат к домену
Для этого в конфиге нужного нам домена в секции `server` находим строку `listen 80;` и ниже неё добавляем:
```text
listen 443 ssl;
include conf/enable/ssl.conf;
ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
```
- Пути к файлам сертификатов заменяем на реальные, полученные в пункте 6.
- Проверяем конфигурацию nginx `sudo nginx -t` и в случае успеха перезапускаем сервер `sudo service nginx reload`

#### Поздравляю с настройкой HTTPS для сайта!
