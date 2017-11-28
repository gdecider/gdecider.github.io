---
title: Изменение свойств заказа на событие перед сохранением | Статьи CMS "1С Битрикс"
keywords: заказ битрикс, свойства заказа, событие перед сохранением заказа
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-onsaleorderbefore-event.html
summary: false
toc: false
---

### Задача

Перед сохранением заказа, проверить значение свойств заказа и в зависимости от полученного значения откоректировать его в соответствии с бизнес-логикой.

### Решение

Для перехвата процесса сохранения заказа есть событие **OnSaleOrderBeforeSaved** [документация](https://dev.1c-bitrix.ru/api_d7/bitrix/sale/events/order_saved.php), именно его нужно использовать для решения поставленой задачи.

Подписываемся на событие:

```php
<?
// Подписываемся на событие в init.php, но лучше выделите под это специальный файл, который подключите в init.php
\Bitrix\Main\EventManager::getInstance()->addEventHandler("sale", "OnSaleOrderBeforeSaved", ["\\MyNameSpace\\OrderEventer", "onOrderSave"]);

// ... и класс с обработчиком в отдельном файле, так же позаботьтесь о подключении его системой

namespace MyNameSpace;

class OrderEventer
{
    public function onOrderSave(Event $event) {
        // получим объект заказа
        $order = $event->getParameter("ENTITY");

        $propertyCollection = $order->getPropertyCollection();

        /** @var \Bitrix\Sale\PropertyValue $obProp */
        foreach ($propertyCollection as $obProp) {
            $arProp = $obProp->getProperty();
            
            // нас интересуют только свойства с кодами "EXPORT_DO", "EXPORT_DO_UR"
            if(!in_array($arProp["CODE"], ["EXPORT_DO", "EXPORT_DO_UR"])) {
                continue;
            }
            
            // Изменим значение свойства на нужное
            if($obProp->getValue() === 'NNN') {
                $obProp->setValue('N');
            } elseif($obProp->getValue() !== 'Y') {
                $obProp->setValue('Y');
            }
        }

    }
}
```

Альтернативный вариант регистрации обработчика событий, используется при установке модулей:

```php
<?
EventManager::getInstance()->registerEventHandler(
    "sale",
    "OnSaleOrderBeforeSaved",
    'myModule.name',
    "\\Local\\Exch1c\\EventerOrder",
    "markOrderForExport"
);
```
