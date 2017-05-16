---
title: Проект | Старт и перенос
keywords: sample homepage
sidebar: bx_sidebar
permalink: bx_struktura-start-and-build.html
summary: false
toc: true
---
## Старт и перенос проекта

### Создание нового проекта

* 1) Скачиваем архив с сайта [Битрикс](https://www.1c-bitrix.ru/download/cms.php#tab-php-link)

* 2) Распаковываем в папку проекта <site_folder>/public

* 3) Создаем файл .gitignore в папке <site_folder>, пример файла [тут](https://gist.github.com/gdecider/a8a7d7071f14dfd220bc32ac96f699ab)

* 4) Создаем файл .gitignore в папке <site_folder>/public/bitrix, пример файла [тут](https://gist.github.com/gdecider/dc55c4a5bd6b515c3097cf1846fd95eb)

* 5) инициализируем основной репозиторий в папке <site_folder>

```bash
git init .
git add .
git rm -r --cached public/bitrix
```

* 6) инициализируем репозиторий ядра битрикс в папке <site_folder>/public/bitrix

```bash
git init .
git add .
git commit -m"init"
```

* 7) Создаем репозитории на bitbucket.org

* 8) Привязываем удаленные репозитории к локальным

```bash
git remote add origin <repo_path>
```

* 9) Проталкиваем изменения ядра в репозиторий

```bash
git push origin --all
```

* 10) Привязываем репозиторий ядра как подмодуль к основному

```bash
git submodule add <core_repo_path> public/bitrix/
```

**Результат должен быть следующим:** ```"Добавляю существующий репозиторий из «public/bitrix» в индекс"```

```bash
git submodule init
```

* 11) Проверяем 

```bash
git submodule status
```

**Результат должен быть следующим:** ```"22996cd787dd1ec3b82724c9ff2f7dc21298ec43 public/bitrix (heads/master)"```

```bash
git status
```

**папка bitrix теперь должна отображаться в логе как файл,** все файлы должны находиться в состоянии "Изменения, которые будут включены в коммит:" и быть "зелеными"

* 12) Делаем коммит основного репозитория и проталкиваем его в bitbucket

```bash
git commit -m"init"
git push origin --all
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