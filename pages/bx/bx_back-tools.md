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
  
* PHP FTP Client Library

  * [https://github.com/altayalp/php-ftp-client](https://github.com/altayalp/php-ftp-client)
  
* bukashk0zzz/yml-generator
  
  Генерация YML (например для Яндекс.Маркет)

  * [https://github.com/Bukashk0zzz/YmlGenerator](https://github.com/Bukashk0zzz/YmlGenerator)
  
* mobiledetect.net

  * [http://mobiledetect.net/](http://mobiledetect.net/)
  
  * [https://github.com/serbanghita/Mobile-Detect](https://github.com/serbanghita/Mobile-Detect)
  
* Реализация Telegram API на PHP 7 с поддержкой асинхронности

  * [https://github.com/unreal4u/telegram-api](https://github.com/unreal4u/telegram-api)
  
* Популярный профайлер XHProf не поддерживается и с PHP 7 работает плохо. Однако доступен обновленный форк от компании Tideways

  * [https://github.com/tideways/php-profiler-extension](https://github.com/tideways/php-profiler-extension)
  
* Безопасный stateless-токен — как JWT, только без его недостатков.

  * [https://github.com/paragonie/past](https://github.com/paragonie/past)
  
* Виджет для вывода ленты Instagram

  * [https://github.com/aik27/inwidget](https://github.com/aik27/inwidget)

### Компоненты и модули Битрикс

* Базовый компонент Битрикс для простой реализации MVC

  * [https://github.com/digitalwand/mvc.base](https://github.com/digitalwand/mvc.base)
  
* Валюты, запрос курса

  * [http://marketplace.1c-bitrix.ru/solutions/asd.currencyrate/](http://marketplace.1c-bitrix.ru/solutions/asd.currencyrate/)

* Форма обратной связи - простой конструктор веб-форм

  * [http://marketplace.1c-bitrix.ru/solutions/altasib.feedback/](http://marketplace.1c-bitrix.ru/solutions/altasib.feedback/)
  
* reCaptchaFree

  * [http://marketplace.1c-bitrix.ru/solutions/twim.recaptchafree/](http://marketplace.1c-bitrix.ru/solutions/twim.recaptchafree/)
  
* Кнопки "Поделиться" соц. сетей
  
  * [https://marketplace.1c-bitrix.ru/solutions/api.yashare/](https://marketplace.1c-bitrix.ru/solutions/api.yashare/)
  
* Параметры заказа в письме

  * [https://marketplace.1c-bitrix.ru/solutions/ipol.mailorder/](https://marketplace.1c-bitrix.ru/solutions/ipol.mailorder/)
  
* Лайки для элементов информационных блоков

  * [https://marketplace.1c-bitrix.ru/solutions/vasoft.likeit/](https://marketplace.1c-bitrix.ru/solutions/vasoft.likeit/)
  
* Дополнительные свойства инфоблоков. Аскарон

  * [https://marketplace.1c-bitrix.ru/solutions/askaron.prop/](https://marketplace.1c-bitrix.ru/solutions/askaron.prop/)
    
* Дополнительные свойства инфоблоков
  
  Модуль по каким-то причинам удалили из маркетплейс, может быть вернут...
  
  * [https://marketplace.1c-bitrix.ru/embx.iblock](https://marketplace.1c-bitrix.ru/embx.iblock)
  
* Модуль миграции сущностей для CMS "1С-Битрикс: Управление сайтом" для разработчиков

  * [https://github.com/intervolga/intervolga.migrato](https://github.com/intervolga/intervolga.migrato)

### Проверка сайта на наличие вредоносного кода

* [AI-Bolit](https://www.revisium.com/aibo/)

### Полезные команды

* Синхронизация файлов и папок локального ПК с сервером. Команда сравнит файлы - скачает новые и удалит на локальной машине то чего нет на сервере, с этим моментом быть внимательным, для отключения удаления уберите флаг ```--delete```

  ```
  rsync -ah --progress --size-only --delete --numeric-ids -e "ssh -T -c arcfour -o Compression=no -x" someuser@somesite.ru:/var/www/some/folder/path/on/serevr/ /some/folder/path/on/your/pc
  ```
  В случае получения ошибки:
  
  ```
  Unable to negotiate with XX.XX.XX.XX port 22: no matching cipher found. Their offer: chacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com,aes128-cbc,aes192-cbc,aes256-cbc,blowfish-cbc,cast128-cbc,3des-cbc
rsync: connection unexpectedly closed (0 bytes received so far) [Receiver]
rsync error: unexplained error (code 255) at io.c(226) [Receiver=3.1.1]
  ```
  
  Эта ошибка означает, что на сервере к которому происходит подключение SSH не поддерживает выбранный протокол шифрования, в данном случае это **arcfour** и сервер приводит список поддерживаемых, нужно выбрать один из поддерживаемых и использовать его, например **-c aes256-ctr**

* Минимизация изображений

  ```
  # Для JPG
  find . -type f -iname '*.jp*g' -print0 | xargs -0 jpegoptim --strip-all --all-progressive --max=85 -t

  # Для PNG
  find . -type f -iname '*.png' -print0 | xargs -0 optipng -strip all -o 1 -v
  ```

* Подсчет количества найденных файлов на примере архивов gz
  
  ```
  find . -type f -name '*.gz' |  wc -l
  ```

* Поиск по содержимому файлов

  ```
  # просто ищем без учета регистра
  grep -iR "some text in file"
  
  # ищем только в файлах с расширением "php"
  grep --include="*.php" -nRHIi "some text in file" .
  
  # описание ключей  
  --include=PATTERN - Recurse in directories only searching file matching PATTERN.
  -n, --line-number - Prefix each line of output with the line number within its input file.
  -R, -r, --recursive - Read all files under each directory, recursively; this is equivalent to the -d recurse option.
  -H, --with-filename - Print the filename for each match.
  -I - Process a binary file as if it did not contain matching data; this is equivalent to the --binary-files=without-match option.
  -i - case-insensitive results.
  ```

* Определение свободного места на сервере

  ```
  # покажет количество места в общем
  df -h
  
  # просмотр размеров по папкам
  du -h --max-depth=1 /path/to/folder/ | sort -n -r
  ```

* Установка прав на файлы и папки

  ```
  # файлы
  sudo find . -type f -exec chmod 664 {} \;
  
  # папки
  sudo find . -type d -exec chmod 775 {} \;
  ```

* Изменение владельца папок

  ```
  sudo chown -R www-data:www-data .
  ```
  
* Подключение к удаленному рабочему столу из Linux в Windows

  ```
  rdesktop -u yourUserName -p yourPass someHostNameOrIP
  ```

* Архивирование/разархивирование

  ```
  # рекурсивно разархивировать все gz в те же папки где они лежат с удалением самого архива
  find . -name "*.gz" | xargs gunzip

  # Простое разархивирование одиночного файла
  tar -xvzf community_images.tar.gz
  tar -xvJf community_images.tar.xz
  
  # Разархивирование архива, поделенного на части
  cat часть_1 часть_2 | tar -xvz
  
  # ИЛИ
  # Если все части архива находятся в одной папке, то команда для склеивания выглядит так:
  cat backup.tar.* | tar -xvz

    # f: this must be the last flag of the command, and the tar file must be immediately after. It tells tar the name and path of the compressed file.
    # z: tells tar to decompress the archive using gzip
    # x: tar can collect files or extract them. x does the latter.
    # v: makes tar talk a lot. Verbose output shows you all the files being extracted.
    
  # To zip up an entire directory (including all subdirectories), type the following command:
  zip -r data *

  # To use unzip to extract all files of the archive pics.zip into the current directory & subdirectories:
  unzip  pics.zip

  # Создание tar.gz архива
  tar [-ключи] [название архива] [путь, что запаковать]
  
  # создать .tar  
  tar -cvf file.tar /full/path
  
  # создать .tar.gz (архив)
  tar -czvf file.tar.gz /full/path
  
  # создать .tar.bz2 (архив)
  tar -cjvf file.tar.bz2 /full/path

  # создать zip
  zip -r file.zip /full/path

  # Распаковать:
  tar -xvf file.tar.gz
  unzip file.zip
  
  Ключи:
  с - (Create) создать архив.
  z – создает архив .tar.gz
  j - создает архив .tar.bz2
  x - (eXtract) позволяет вам извлекать файлы из архива.
  v - делает вывод tar подробным. Это означает, что на экран будут выведены все найденные в архиве файлы. Если эта опция опущена, информация, выводимая в процессе обработки, будет ограничена.
  f - является обязательной опцией. Без неё tar пытается использовать магнитную ленту вместо файла архива.
  z - позволяет вам обрабатывать архив, сжатый gzip'ом (с расширением .gz). Если вы забудете указать эту опцию, tar выдаст ошибку. И наоборот, эта опция не должна использоваться для несжатых архивов.
  t - (Test) просмотреть содержимое архива.

  # Архивировать по кускам
  tar cvzf - dir/ | split --bytes=200MB - sda1.backup.tar.gz.

  # В случае ошибки "split: illegal option -- -"
  tar cvzf - dir/ | split -b 1024MB - sda1.backup.tar.gz.
  
  #If you happen to be trying to split file to fit on a FAT32 formatted drive use a byte limit of 4294967295. For example:
  tar cvzf - /Applications/Install\ macOS\ Sierra.app/ | \
  split -b 4294967295 - /Volumes/UNTITLED/install_macos_sierra.tgz.
  ```
* Передача файлов на Яндекс.Диск
  
  ```
  curl --user USER:PASSWORD -T "file1" https://webdav.yandex.ru/
  curl --user USER:PASSWORD -T "{file1,file2,...}" https://webdav.yandex.ru/
  curl --user USER:PASSWORD -T "file[1-5].ext" https://webdav.yandex.ru/
  ```
  
### Подборка статей

* Чек-лист проверки сайта на устойчивость

  * [https://simplesecurity.sensedeep.com/web-developer-security-checklist-f2e4f43c9c56](https://simplesecurity.sensedeep.com/web-developer-security-checklist-f2e4f43c9c56)

* Autocomplete Composer script names on the command line

  Статья и пример скрипта

  * [https://akrabat.com/autocomplete-composer-script-names-on-the-command-line/](https://akrabat.com/autocomplete-composer-script-names-on-the-command-line/)
