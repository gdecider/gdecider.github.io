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
