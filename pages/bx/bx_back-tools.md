---
title: Back-end Инструменты
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_back-tools.html
summary: false
toc: false
---

## Back-end Инструменты

* bxApiDocs

  Документированное ядро битрикса для подключения к IDE. PhpStorm В настройках PHP IDE PhpStorm (File -> Settings -> Project Settings -> PHP или File -> Settings -> Languages & Frameworks -> PHP) области Include Path нажав на "+" добавляем путь к папке modules.

  * [https://github.com/matiaspub/bxApiDocs](https://github.com/matiaspub/bxApiDocs)

* Imagery

  Работа с изображениями

  * [https://github.com/wapmorgan/Imagery](https://github.com/wapmorgan/Imagery)

* PHPMailer

  Отправка почты

  * [https://github.com/PHPMailer/PHPMailer](https://github.com/PHPMailer/PHPMailer)

* reCAPTCHA PHP client library

  * [https://github.com/google/recaptcha](https://github.com/google/recaptcha)

* PHP dotenv

  * [https://github.com/vlucas/phpdotenv](https://github.com/vlucas/phpdotenv)

### Компоненты и модули Битрикс

* Базовый компонент Битрикс для простой реализации MVC

  * [https://github.com/digitalwand/mvc.base](https://github.com/digitalwand/mvc.base)
  
* Валюты, запрос курса

  * [http://marketplace.1c-bitrix.ru/solutions/asd.currencyrate/](http://marketplace.1c-bitrix.ru/solutions/asd.currencyrate/)

* Форма обратной связи - простой конструктор веб-форм

  * [http://marketplace.1c-bitrix.ru/solutions/altasib.feedback/](http://marketplace.1c-bitrix.ru/solutions/altasib.feedback/)
  
* reCaptchaFree

  * [http://marketplace.1c-bitrix.ru/solutions/twim.recaptchafree/](http://marketplace.1c-bitrix.ru/solutions/twim.recaptchafree/)

### Проверка сайта на наличие вредоносного кода

* [AI-Bolit](https://www.revisium.com/aibo/)

### Полезные команды

* Синхронизация файлов и папок локального ПК с сервером. Команда сравнит файлы - скачает новые и удалит на локальной машине то чего нет на сервере, с этим моментом быть внимательным, для отключения удаления уберите флаг ```--delete```

  ```
  rsync -ah --progress --size-only --delete --numeric-ids -e "ssh -T -c arcfour -o Compression=no -x" someuser@somesite.ru:/var/www/some/folder/path/on/serevr/ /some/folder/path/on/your/pc
  ```

* Минимизация изображений

```
# Для JPG
find . -type f -iname '*.jp*g' -print0 | xargs -0 jpegoptim --strip-all --all-progressive --max=85 -t

# Для PNG
find . -type f -iname '*.png' -print0 | xargs -0 optipng -strip all -o 1 -v
```

### Подборка статей

* Чек-лист проверки сайта на устойчивость

  * [https://simplesecurity.sensedeep.com/web-developer-security-checklist-f2e4f43c9c56](https://simplesecurity.sensedeep.com/web-developer-security-checklist-f2e4f43c9c56)

* Autocomplete Composer script names on the command line

  Статья и пример скрипта

  * [https://akrabat.com/autocomplete-composer-script-names-on-the-command-line/](https://akrabat.com/autocomplete-composer-script-names-on-the-command-line/)
