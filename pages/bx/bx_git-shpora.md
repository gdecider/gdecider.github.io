---
title: Git Шпаргалка | Список часто используемых команд
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_git-shpora.html
summary: false
toc: true
---

## Git Список часто используемых команд

### Установка

```bash
sudo apt-get update
sudo apt-get install git
```

### Рекомендуемые утилиты

* [https://www.gitkraken.com/](https://www.gitkraken.com/)

### Справочник по командам

#### Первоначальная настройка

```bash
# установка имени и почты
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com

# отключить fast-forward при merge (начиная с git version 1.7.6)
git config --global merge.ff false
# разрешить fast-forward при pull, т.к. pull == featch+merge  (начиная с git version 1.7.6)
git config --global pull.ff yes

# установка редактора
git config --global core.editor subl

# проверка настроек общая
git config --list

# проверка настроек конкретного параметра
git config user.name

# для удаления настройки
git config --global --unset <param.name>
```

#### Общие команды

* инициализация

```bash
git init
```

* добавление к будущему комиту

```bash
git add .
git add --all
```

* коммит

```bash
git commit -m"комментарий к комиту"
```

#### Работа с удаленными репозиториями

* добавление привязки удаленного репозитория к проекту

```bash
git remote add somename <path-to-repo>
```

* удаление привязки к удаленному репозиторию

```bash
git remote rm somename
```

* изменение ссылки на удаленный репозиторий

```bash
git remote set-url origin git://new.url.here
```

* просмотр списка удаленных репозиториев

```bash
git remote -v
```

* изменение адреса удаленного репозитория

```sh
git remote set-url origin https://github.com/USERNAME/OTHERREPOSITORY.git
```

* привязать локальную ветку к удаленной для отслеживания

```bash
git branch --set-upstream-to=origin/<branch> <local_branch_name>
```

* получение удаленной ветки в локальный репозиторий

```bash
git branch -f remote_branch_name origin/remote_branch_name
git checkout remote_branch_name
```

* пуш коммита в удаленный репозиторий

```bash
git push origin master
```

* получение данных из удаленного репозитория

```bash
git pull
# или
git pull origin
# или
git pull origin master
```

* удаление удаленной ветки

```bash
git push origin --delete <branchName>
```

* обновление данных об удаленных ветках

```bash
git fetch -p
```

#### Откаты и восстановления

* откатить не проиндексированный файл (до добавления в список перед коммита)

```bash
git checkout <filename>
```

* откатить проиндексированный файл (до коммита)

```bash
git reset HEAD <filename> // это сбросит проиндексированные файлы, но изменения в файле не откатятся
git checkout <filename> // это откатит сами изменения
```

* Удаление неотслеживаемых файлов ПРОСМОТР списка удалений

```bash
git clean -d -n
```

* Удаление неотслеживаемых файлов + те которые добавлены в игнор ПРОСМОТР списка удалений
**!!! осторожно . удалит и файлы настроек, т.к. они всегда в игноре**

```bash
git clean -d -n -x
```

* Исправить последний коммит

```bash
# исправить только текст коммита
git --amend -m"New commint text"

# переключиться на родительский коммит и исправить файлы
git reset HEAD^
# правим файлы, добавляем как обычно и коммитим
git --amend -m"New commint text"
```

* Удалить последние n коммитов

Специальной команды для этого нет, нужно выполнить ряд действий:  
  
```bash
# переключиться на коммит к которому мы хотим откатить ветку
git checkout <needed commit hash>

# создать новую ветку от текущего коммита и переключиться на нее
git checkout -b new_tmp_branch

# удалить старую ветку
git branch -d old_branch_name

# переименовать новую ветку в старое имя
git branch -m new_tmp_branch old_branch_name

# при необходимости прокинуть ветку в удаленный репозиторий и назначить отслеживание локальной веткой удаленной
```  

#### Прятанье [источник](https://git-scm.com/book/ru/v1/%D0%98%D0%BD%D1%81%D1%82%D1%80%D1%83%D0%BC%D0%B5%D0%BD%D1%82%D1%8B-Git-%D0%9F%D1%80%D1%8F%D1%82%D0%B0%D0%BD%D1%8C%D0%B5)

* спрятать изменения без коммита

```bash
git stash
```

* просмотр списка спрятанных изменениц

```bash
git stash list
```

* достать спрятанные изменения

```bash
# восстановить последние спрятанные данные
git stash apply

# восстановить последние спрятанные данные, без добавления файлов в индекс
git stash apply --index

# восстановить выбранные спрятанные данные
git stash apply stash@{2}
```

* удалить спрятанные изменения

```bash
git stash drop stash@{0}
```

#### Работа с ветками

* создание ветки

```bash
git branch new-branch
```

* переключение на ветку

```bash
git checkout new-branch
```

* создание ветки и переключение на нее в одну команду

```bash
git checkout -b new-branch
```

* удаление ветки

```bash
git branch -d branch-name
```

* переименование ветки

```bash
git branch -m old_name new_name
```

* просмотр веток

```bash
git branch -v
```

* просмотр всех веток вместе с удаленными

```bash
git branch -a
```

* просмотр только удаленных веток

```bash
git branch -r
```

* совместный просмотр и локальных и удаленных веток с подробной информацией

```bash
git branch -avv
```

* просмотр веток, влитых в текущую

```bash
git branch --merged
```

* просмотр веток, не влитых в текущую

```bash
git branch --no-merged
```

* Слияние веток

```bash
# все вливается в текущую ветку на которой вы находитесь, поэтому перед слиянием переключитесь на нужную ветку
git checkout branch1-name

# льем другую ветку в текущую
git merge --no-ff branch2-name

# если после слияния ветка не нужна, то можно ее удалить 
git branch -d branch2-name
```

#### Работа с метками (тегами) [источник](https://git-scm.com/book/ru/v1/%D0%9E%D1%81%D0%BD%D0%BE%D0%B2%D1%8B-Git-%D0%A0%D0%B0%D0%B1%D0%BE%D1%82%D0%B0-%D1%81-%D0%BC%D0%B5%D1%82%D0%BA%D0%B0%D0%BC%D0%B8)

* просмотр списка тегов

```bash
git tag
```

* поиск по маске

```bash
git tag -l 'v1.4.*'
```

* создание аннотированной метки

```bash
git tag -a v1.4 -m 'my version 1.4'
```

* создание легковесной метки

```bash
git tag v1.4
```

* добавление метки на созданный ранее коммит

```bash
git tag -a v1.2 -m 'version 1.2' [код коммита]
```

* проталкивание данных о метках в удаленный репозиторий

```bash
# все
git push origin --tags

# одну конкретную
git push origin v1.5
```

#### Просмотр логов

* просмотр лога

```bash
https://git-scm.com/book/ru/v1/%D0%9E%D1%81%D0%BD%D0%BE%D0%B2%D1%8B-Git-%D0%9F%D1%80%D0%BE%D1%81%D0%BC%D0%BE%D1%82%D1%80-%D0%B8%D1%81%D1%82%D0%BE%D1%80%D0%B8%D0%B8-%D0%BA%D0%BE%D0%BC%D0%BC%D0%B8%D1%82%D0%BE%D0%B2
git log
git log -p
git log --pretty=oneline --graph
git log --pretty=format:"%h - %an, %ar : %s"
gitk //это GUI утилита, в линукс потребуется установка через sudo apt-get install gitk
```

* список коммитов, сделанных за последние две недели

```bash
git log --since=2.weeks 
// так же можно указать дату например "2008-01-15", 
// Опция --author позволяет фильтровать по автору, опция --grep позволяет искать по ключевым словам в сообщении
```

#### Сравнение изменений

* сравнение текущих изменений с последним комитом

```bash
# все файлы
git diff HEAD

# определенный файл
git diff HEAD fileName.ext
```

* сравнение комитов между собой

```bash
git diff [commit_code_1] [commit_code_2]
```

* просмотр объема правок перед коммитом

```bash
git count-objects -vH
```


#### Работа с подмодулями

* привязка подмодуля

```bash
git submodule add <submodule_repo_path> path/to/submodule/folder
```

* первичная инициализация подмодулей

```bash
git submodule init
```

* получение/обновление содержимого подмодулей

```bash
git submodule update
```

* просмотр списка подмодулей

```bash
git submodule status
```

* Удаление подмодуля

```shell
mv a/submodule a/submodule_tmp
git submodule deinit -f -- a/submodule    
rm -rf .git/modules/a/submodule

git rm -f a/submodule
# Note: a/submodule (no trailing slash)

# or, if you want to leave it in your working tree
git rm --cached a/submodule
mv a/submodule_tmp a/submodule
```

В случае получения ошибки "already exists in the index" при добавлении подмодуля:
 
```bash
#просмотреть наличие проиндексированных данных
git ls-files --stage projectfolder

# удалить данные из индекса
git rm --cached projectfolder

# повторно пытаемся добавить подмодуль
git add submodule <repo> <path>
```


#### Оптимизация репозитория

* Очистка логов

```bash
git reflog expire --all --expire=now
```

* Запуск сборщика мусора

```bash
git gc --prune=now --aggressive
```

### Материалы для обучения

* [Пошаговый учебник](https://githowto.com/ru/)
* Видеокурс "Git для профессионалов" от [http://pr-of-it.ru](http://pr-of-it.ru) найдете при желании :)
* [Статья о rebase и слиянии веток](https://habrahabr.ru/post/161009/)
* Обучающие видео [http://monsterlessons.com/project/categories/git](http://monsterlessons.com/project/categories/git)
* Полезные алиасы git команд [https://habrahabr.ru/company/mailru/blog/318508/?utm_campaign=email_digest&utm_source=email_habrahabr&utm_medium=email_week_20170110&utm_content=link2post](https://habrahabr.ru/company/mailru/blog/318508/?utm_campaign=email_digest&utm_source=email_habrahabr&utm_medium=email_week_20170110&utm_content=link2post)
* Шпаргалка по работе с Git [http://eax.me/git-commands/](http://eax.me/git-commands/)

### установка средства сравнения 

* [https://nathanhoad.net/how-to-meld-for-git-diffs-in-ubuntu-hardy](https://nathanhoad.net/how-to-meld-for-git-diffs-in-ubuntu-hardy)

0.1) устанавливаем

sudo aptitude install meld

0.2) создаем скрипт питона

cd ~/scriptHelpers
touch diff.py

```
#!/usr/bin/python

import sys
import os

os.system('meld "%s" "%s"' % (sys.argv[2], sys.argv[5]))
```

0.3) прописываем в настройках гита

git config --global diff.external ~/scriptHelpers/diff.py
что бы отменить эту настройку используем git config --global --unset diff.external

Проверить что настройки применились
git config --list

3) создание репозитория
git init

добавление удаленного репозитория к текушему
git remote add origin https://github.com/gdecider/tscc.git

проталкивание изменений в удаленный репозиторий
git push -u origin master

клонирование репозитория в текущую папку
git clone https://github.com/gdecider/tscc.git .

удаление файлов из подготовленных к коммиту
git rm --cached <file name>
если это папка, то добавляем флаг -r для рекурсивного удаления

(http://uleming.github.io/gitbook/4_%D0%9E%D1%82%D0%BC%D0%B5%D0%BD%D0%B0_%D0%B2_git_-_%D0%A1%D0%B1%D1%80%D0%BE%D1%81,_%D0%98%D0%B7%D0%B2%D0%BB%D0%B5%D1%87%D0%B5%D0%BD%D0%B8%D0%B5_%D0%B8_%D0%9E%D1%82%D0%BA%D0%B0%D1%82.html)
(https://www.prolinux.org/post/korotkii-spravochnik-po-git-komandam/)

git reset --hard HEAD
git clean -f

Это отбросит все сделанные изменения которые вы возможно добавили в индекс git, а также все другие изменения в вашей рабочем дереве. Другими словами, результат этого - вывод команд "git diff" и "git diff --cached" будет пустым.

About MELD http://meldmerge.org/help/

1) Installing Meld on Ubuntu (http://linuxpitstop.com/install-meld-on-ubuntu-and-mint-linux/)

sudo apt-get update
sudo apt-get install meld

```
настройки можно внести в глобальный файл настроек гита, который находится ~/.gitconfig
[user]
	name = decider_op
	email = decider@ya.ru
[core]
	editor = subl -n -w
[diff]
	tool = meld
[difftool]
	prompt = false
[merge]
	tool = meld
[mergetool]
	keepBackup = false

```

или через команды

git config --global diff.tool meld
git config --global merge.tool meld
.
.
.
и т.д.


--- !!!то что ниже не проверено

git config --global mergetool.meld.trustExitCode false

// Fix git mergetool meld --help error
git config mergetool.meld.hasOutput true


Для доступа к репозиторию нужно добавить SSH ключ в настройках аккаунта github
https://help.github.com/articles/connecting-to-github-with-ssh/

0) Проверим есть ли ключ на ПК с которого нужно соединиться
ls -al ~/.ssh

1) Сгенерируем ключ, если нет существующего
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

На вопрос об имени файла можно оставить имя по умолчанию

На вопрос о пароле введите пароль или оставьте его пустым

2) Привязка ключа в аккаунт github
Для получения содержимомо ключа выведем его на экран

cat ~/.ssh/id_rsa.pub

Копируем то что вывелось в буфер обмена

Можно воспользоваться утилитой xcopy

В Вашем аккаунте GitHub идем в настройки, пункт SSH and GPG keys - New SSH key, вставляем скопированный ключ.


### Добавление информации о публичном ключе на сервер

#### Вариант 1

```sh
ssh-copy-id <user_name>@<server_ip_on_domain>
```

#### Вариант 2

```sh
cat ~/.ssh/<your_ssh_key_name>.pub | ssh <user_name>@<server_ip_on_domain> "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
```




