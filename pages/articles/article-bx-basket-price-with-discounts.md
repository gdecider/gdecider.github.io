---
title: Статьи 1С Битрикс | Итоговая стоимость корзины с учетом скидок
keywords: битрикс корзина, цена с учетом скидки
sidebar: articles_sidebar
folder: articles
permalink: article-bx-basket-price-with-discounts.html
summary: false
toc: false
---

## Итоговая стоимость корзины с учетом скидок

### Задача

Получить стоимость корзины с учетом скидок

### Проблема

С какогото момента ```$basket->getPrice()``` стало возвращать стоимость корзины **БЕЗ** учета скидок,
по этому понадобился иной метод, он создает фейковый заказ без сохранения и актуальная цена берется уже из заказа,
а не из корзины.

Данный подход описан по следующим ссылкам: 
* [https://dev.1c-bitrix.ru/support/forum/messages/forum6/topic98143/message497597/#message497597](https://dev.1c-bitrix.ru/support/forum/messages/forum6/topic98143/message497597/#message497597), 
* [https://dev.1c-bitrix.ru/community/webdev/user/186370/blog/20897/?commentId=107351#com107351](https://dev.1c-bitrix.ru/community/webdev/user/186370/blog/20897/?commentId=107351#com107351)

### Решение

```php
<?php
class Basket
{
    public function __construct()
    {
        Loader::includeModule('iblock');
        Loader::includeModule('sale');
        $this->siteId = \Bitrix\Main\Context::getCurrent()->getSite();
        $this->fuserId = Sale\Fuser::getId();
    }

    /**
     * Получение стоимости корзины с учетом скидок
     *
     * @return float
     * @throws \Bitrix\Main\ArgumentException
     * @throws \Bitrix\Main\ArgumentNullException
     * @throws \Bitrix\Main\ArgumentOutOfRangeException
     * @throws \Bitrix\Main\NotImplementedException
     * @throws \Bitrix\Main\NotSupportedException
     * @throws \Bitrix\Main\ObjectException
     * @throws \Bitrix\Main\ObjectNotFoundException
     */
    public function getBasketPrice()
    {
        $basket = Sale\Basket::loadItemsForFUser($this->fuserId, $this->siteId);
        $order = Sale\Order::create($this->siteId, $this->fuserId);
        $order->setPersonTypeId(1);
        $order->setBasket($basket);

        $discounts = $order->getDiscount();
        $discounts->getApplyResult();
    
        return $order->getPrice();
    }
}
```
