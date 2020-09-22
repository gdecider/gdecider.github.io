---
title: Статьи 1С Битрикс | Кастомизация административного раздела
keywords: битрикс, административный раздел, админка, кастомизация, внедрение правок
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-admin-customisation.html
summary: Пример внесения правок в страницы административного раздела для CMS 1С Битрикс
toc: true
---

## Задача

Добавить вывод произвольного содержимого на страницу вывода информации о заказах в административном разделе Битрикс без кастомизации ядра системы.

## Решение

* В init.php, а лучше в подключаемом в нем файле:
  * подписываемся на событие ```OnAdminTabControlBegin``` (событие OnAdminTabControlBegin вызывается в функции CAdminTabControl::Begin() при выводе в    административном интерфейсе формы редактирования).
  * подписываемся на событие ```OnAdminListDisplay``` (Событие OnAdminListDisplay вызывается в функции CAdminList::Display() при выводе в административном разделе списка элементов).

```php
<?php
use \Bitrix\Main\EventManager;
$eventManager = EventManager::getInstance();
$eventManager->addEventHandler('main', 'OnAdminTabControlBegin', ['\LocalNamespase\Handlers', 'onAdminTabControlBegin']);
$eventManager->addEventHandler('main', 'OnAdminListDisplay', ['\LocalNamespase\Handlers', 'onAdminListDisplay']);
```

* В обработчиках событий внедряем нужные js-скрипты

```php
<?php
public static function onAdminTabControlBegin(&$form)
{
    static $init;
    if (!$_REQUEST['ID']) {
        return;
    }
    switch ($form->name) {
        case 'order_edit_info':
            // защита от повторной инициализации
            if ($init[$form->name]) {
                return;
            }

            // внедряем нужный скрипт
            Asset::getInstance()->addJs('/local/templates/index/js/some_script.js');
            break;
    }
}

public static function onAdminListDisplay(&$list)
{
    global $APPLICATION;

    switch ($list->table_id) {
        case 'tbl_sale_buyers_profile_tab1':
            // внедряем нужный скрипт
            Asset::getInstance()->addJs('/local/templates/index/js/some_script.js');
            break;
    }
}
```

* Во внедренном js-скрипте делаем нужные действия, например отправим ajax запрос, получим данные, выведем их в нужном нам месте, само место вывода можно определить на основе DOM-дерева разметки административной страницы, как правило в административных страницах БУС часто используются уникальные id для элементов интерфейса.


