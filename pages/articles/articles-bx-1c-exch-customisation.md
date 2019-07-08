---
title: Кастомизация данных при обмене с 1С | Статьи CMS "1С Битрикс"
keywords: 1с битрикс, Кастомизация данных
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-1c-exch-customisation.html
summary: false
toc: false
---

### Задача

Кастомизировать данные при выполнении обмена сайта с 1С

### Решение

#### Вариант 1. Обработка на уровне событий добавления и обновления элемента ИБ.

```php
<?php
// Добавляем события 
$em = Bitrix\Main\EventManager::getInstance();

$em->addEventHandler('iblock', 'OnBeforeIBlockElementAdd', [
    EventHandlers::class,
    'on1CUserAddElement'
]);

$em->addEventHandler('iblock', 'OnBeforeIBlockElementUpdate', [
    EventHandlers::class,
    'on1CUserUpdateElement'
]);

// Добавляем обработчик события
class EventHandlers
{
    public static function on1CUserAddElement(array &$arFields)
    {
        // проверяем, что это нужный ИБ
        if ($arFields['IBLOCK_ID'] !== Config::IB_CATALOG) {
            return;
        }
        // проверяем, что это выгрузка, а не правка в админке
        if(!isset($_GET['type'], $_GET['mode'], $_GET['filename'])) {
			      return;
		    }

        // тут выполняем нужные действия

        return;
    }
    
    public static function on1CUserUpdateElement(array &$arFields)
    {
        // проверяем, что это нужный ИБ
        if ($arFields['IBLOCK_ID'] !== Config::IB_CATALOG) {
            return;
        }
        // проверяем, что это выгрузка, а не правка в админке
        if(!isset($_GET['type'], $_GET['mode'], $_GET['filename'])) {
			      return;
		    }

        // тут выполняем нужные действия

        return;
    }
}
```
