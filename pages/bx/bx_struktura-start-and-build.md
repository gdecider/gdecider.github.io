---
title: Проект | Старт и перенос
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_struktura-start-and-build.html
summary: false
toc: true
---
## Старт и перенос проекта

### Создание нового проекта

* 1) Скачиваем архив с сайта [Битрикс](https://www.1c-bitrix.ru/download/cms.php#tab-php-link)

* 2) Проводим стандартную установку Битрикс (**Помним** о "Установить в кодировке UTF-8", Тип таблиц базы данных: Innodb)

* 3) Распаковываем в папку проекта ```<site_folder>/public```

* 4) Создаем файл .gitignore в папке ```<site_folder>```, пример файла [тут](https://gist.github.com/gdecider/a8a7d7071f14dfd220bc32ac96f699ab)

  <script src="https://gist.github.com/gdecider/a8a7d7071f14dfd220bc32ac96f699ab.js"></script>

* 5) Создаем файл .gitignore в папке ```<site_folder>/public/bitrix```, пример файла [тут](https://gist.github.com/gdecider/dc55c4a5bd6b515c3097cf1846fd95eb)

  <script src="https://gist.github.com/gdecider/dc55c4a5bd6b515c3097cf1846fd95eb.js"></script>

* 6) Копируем примеры файлов конфигов, что бы не заморачиваться при переносе проекта между репозиториями разработчиков.

  ```
  # файл примера настройки нового ядра
  /bitrix/.settings.example.php

  # файл примера настройки старого ядра
  /bitrix/php_interface/dbconn.example.php
  ```

  [код файлов](https://gist.github.com/gdecider/90e61867887e2671675878f0f96a5bb9)

  при переносе проекта эти файлы копируются, уберается ```.example``` в имени файла и в них вносятся нужные настройки.
  
  <script src="https://gist.github.com/gdecider/90e61867887e2671675878f0f96a5bb9.js"></script>

* 7) инициализируем основной репозиторий в папке ```<site_folder>```

  ```bash
  git init .
  git add .
  git rm -r --cached public/bitrix
  ```

* 8) инициализируем репозиторий ядра битрикс в папке ```<site_folder>/public/bitrix```

  ```bash
  git init .
  git add .
  git commit -m"init"
  ```

* 9) Создаем репозитории на [bitbucket.org](https://bitbucket.org/)

  **Имя репозитория составляем по маске:**

  ```
  <projectType>-<projectName>[-<coreName>]

  где
    <projectType> - тип проекта:
      im - интернет-магазин
      lp - лендинг пейдж
      st - прочие сайты, такие как каталоги товаров, сайты визитки и иные...

    <projectName> - имя проекта

    <coreName> - имя ядра системы, присваивается репозиториям, 
                 которые выступают как подмодули основного репозитория
                 пример: bitrix, modx, wp

  ```

* 10) Привязываем удаленные репозитории к локальным

  ```bash
  git remote add origin <repo_path>
  ```

* 11) Проталкиваем изменения ядра в репозиторий

  ```bash
  git push origin --all
  ```

* 12) Привязываем репозиторий ядра как подмодуль к основному

  ```bash
  git submodule add <core_repo_path> public/bitrix/
  ```

**Результат должен быть следующим:** ```"Добавляю существующий репозиторий из «public/bitrix» в индекс"```

  ```bash
  git submodule init
  ```

* 13) Проверяем 

  ```bash
  git submodule status
  ```

**Результат должен быть следующим:** ```"22996cd787dd1ec3b82724c9ff2f7dc21298ec43 public/bitrix (heads/master)"```

  ```bash
  git status
  ```

**папка bitrix теперь должна отображаться в логе как файл,** все файлы должны находиться в состоянии "Изменения, которые будут включены в коммит:" и быть "зелеными"

* 14) Получаем шаблон локального модуля для инкапсуляции кода проекта

  ```bash
  cd public/local
  mkdir -p modules/local.common
  cd modules/local.common

  git clone https://ariets@bitbucket.org/ariets/bxmodule-local.common.git .
  ```
  После получения файлов идем в админку битрикса и устанавливаем модуль в разделе сторонних модулей маркетплейс

* 15) Делаем коммит основного репозитория и проталкиваем его в bitbucket

  ```bash
  git commit -m"init"
  git push origin --all
  ```

* 16) Привязываем отслеживание веток

  ```bash
  # основной репозиторий
  git branch --set-upstream-to=origin/master master

  # подмодуль ядра
  cd public/bitrix
  git branch --set-upstream-to=origin/master master
  ```

### Получение существующего проекта

  ```bash
  cd <site_path>
  git clone <repo path> .
  git submodule init bitrix
  git submidule update
  cd bitrix
  git checkout master
  cd ..
  git status
  ```
**папка bitrix теперь должна отображаться в логе как файл**
