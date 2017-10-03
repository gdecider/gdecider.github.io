---
title: Быстрая настройка окружения для Битрикс на VDS на основе VM Bitrix | Статьи Рабочее окружение
sidebar: articles_sidebar
keywords: bitrix vm vds
permalink: articles-env-bitrix-vm-fast-setup-vds.html
toc: false
folder: articles
---

## Установка Bitrix VM

Поддержка битрикса предоставляет скрипт для быстрой установки окружения на VDS. 
Для его получения и запуска нужно выполнить комманды:

```sh
wget http://repos.1c-bitrix.ru/yum/bitrix-env.sh
chmod +x bitrix-env.sh
./bitrix-env.sh 
```
После выполнения этих команд будет выведена информация о проделаной работе, среди этой информации будет строка **You can find root password at /root/.my.cnf client config file.** забираем пароль для БД и удаляем этот файл.
