---
title: Отмена отправки письма о новом заказе | Статьи CMS "1С Битрикс"
keywords: email битрикс, отмена отправки события
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-new-order-email-send-cancel.html
summary: false
toc: false
---

### Задача

Отменить отправку письма о новом заказе в биртикс, если нет данных о составе заказа.
Иногда БУС отправляет 2а письма при оформлении заказа, так можно отменить отправку лишнего письма.

### Решение

**init.php**

```php
$eventManager = Bitrix\Main\EventManager::getInstance();

$eventManager->addEventHandler(
    'sale',
    'OnOrderNewSendEmail',
    [\Some\Handlers::class,'OnOrderNewSendEmail']
);
```

**Ваш произвольный класс handlers.php**

```php
namespace Some;

class Handlers
{
    public static function OnOrderNewSendEmail($orderID, &$eventName, &$arFields)
    {
        // отменяем системную отправку письма о новом заказе,
        // если нет данных о составе заказа
        return !empty($arFields['ORDER_LIST']);
    }
}
```
