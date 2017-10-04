---
title: Настройка работы Агентов Битрикс по cron | Статьи CMS "1С Битрикс"
keywords: работа агентов битрикс по cron
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-set-agents-on-cron.html
summary: false
toc: false
---

## Настройка

* Добавить константу-флаг ```define('BX_CRONTAB_SUPPORT', true);``` в файл ```<site_path>/bitrix/dbconn.php```, что бы система поняла, что агентов нужно выполнять по заданию cron.

* Добавить в крон задание для мониторинга агентов

Открыть файл заданий для редактирования:

```
crontab -e
```

Добавить новую строку с заданием:

```
*/1 * * * * php <site_path>/bitrix/modules/main/tools/cron_events.php
```

Подробнее о работе с cron можно узнать [тут](/articles-env-cron-howto.html)
