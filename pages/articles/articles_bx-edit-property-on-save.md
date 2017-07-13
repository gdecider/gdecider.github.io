---
title: Статьи 1С Битрикс | Изменить значение свойства товара при сохранении
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-edit-property-on-save.html
summary: false
toc: true
---

## Изменить значение свойства товара при сохранении

### Задача

При сохранении элемента инфоблока вычислить минимальную стоимость элемента 
на основании нескольких свойств цены и записать это значение в отдельное свойство.
При этом инфоблок является каталогом и одно из свойств от которого зависят результаты вычислений - тип цены товара.

### Решение

Описанные в задаче действия нужно выполнять в 2х случаях: при создании элемента инфоблока и при его изменении.
Так же нужно учесть, что указанные операции нужно выполнять не на всех, а только на нужных инфоблоках.

В обычном случае для инфоблоков используются события:

  * Перед созданием элемента [OnBeforeIBlockElementAdd](https://dev.1c-bitrix.ru/api_help/iblock/events/onbeforeiblockelementadd.php)
  * Перед изменением элемента [OnBeforeIBlockElementUpdate](https://dev.1c-bitrix.ru/api_help/iblock/events/onbeforeiblockelementupdate.php)

Но в условиях нашей задачи реч идет о товарах и о значении типа цены товара. 
В момент создания и обновления элемента инфоблока данных о его цене еще нет и получить мы их в эти моменты не можем.
Поэтому нам нужно использовать события:

  * Для добавления [OnPriceAdd](https://dev.1c-bitrix.ru/api_help/catalog/events/onpriceadd.php)
  * Для создания [OnPriceUpdate](https://dev.1c-bitrix.ru/api_help/catalog/events/onpriceupdate.php)

#### Создание обработчиков событий



```php

```
