---
title: Статьи Рабочее окружение | Решение проблем
sidebar: articles_sidebar
keywords: news, blog, updates, release notes, announcements
permalink: articles_env-reshenie-problem.html
toc: false
folder: articles
---

## Статьи Рабочее окружение | Решение проблем

### Восстанавливаем работу горячих клавиш в PHPStorm в русской расскладке

[Первоисточник](https://www.kobzarev.com/soft/chinim-goryachie-klavishi-phpstorm-v-russkoy-raskladke-pod-ubuntu/)

```bash
git clone https://github.com/zheludkovm/LinuxJavaFixes.git fix

// Путь зависить от Вашей версии приложения, главное найти phpstorm64.vmoptions
nano ~/PhpStorm-163.10504.2/bin/phpstorm64.vmoptions

// Так же указываем верный путь именно для вашей системы
-javaagent:/home/user/fix/build/LinuxJavaFixes-1.0.0-SNAPSHOT.jar
```

### PHPStorm ошибка "User limit of inotify watches reached" в Ubuntu 16.04

* Проверяем текущее ограничение на количество вотчеров

```bash
cat /proc/sys/fs/inotify/max_user_watches

# результат: 8192
```

* Увеличиваем количество вотчеров до нужного нам

```bash
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p --system
```

Изменения для IDE вступят в силу при её следующем запуске.

### Не работает авторизация Битрикс

Тут явная проблема в работе механизма сессий

* Пробуем поставить галку "Запомнить меня" и авторизоваться с ней, если получилось, тогда идем в ```Настройки -> Производительность -> PHP -> Настройки PHP```, далее ищем группу настроек "session" и проверяем корректность настроек.

Особое внимание следует обратить на параметр **session.save_path** в нем указан путь, по которому происходит сохранение файлов сессий для проекта. Нужно пройти по указанному пути, проверить его существование и права доступа и записи для указанной папки пользователем из-под которого работает Apache.
