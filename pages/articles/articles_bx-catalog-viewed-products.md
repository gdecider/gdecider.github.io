---
title: Статьи 1С Битрикс | Вывод ранее просмотренных товаров
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-catalog-viewed-products.html
summary: false
toc: true
---

## Вывод ранее просмотренных товаров

### Задача

Вывести список ранее просмотренных посетителем сайта товаров.

### Решение

Для вывода списка ранее просмотренных товаров у Битрикса есть специально обученный компонент **bitrix:catalog.viewed.products**.

**Пример подключения компонента**

```php
<?$APPLICATION->IncludeComponent("bitrix:catalog.viewed.products", "viewed_products", Array(
		"ACTION_VARIABLE" => "action_cvp",	// Название переменной, в которой передается действие
		"ADDITIONAL_PICT_PROP_8" => "MORE_PHOTO",	// Дополнительная картинка
		"ADD_PROPERTIES_TO_BASKET" => "Y",	// Добавлять в корзину свойства товаров и предложений
		"BASKET_URL" => "/cart/",	// URL, ведущий на страницу с корзиной покупателя
		"CACHE_GROUPS" => "Y",	// Учитывать права доступа
		"CACHE_TIME" => "36000000",	// Время кеширования (сек.)
		"CACHE_TYPE" => "A",	// Тип кеширования
		"CART_PROPERTIES_8" => array(	// Свойства для добавления в корзину
			0 => "",
			1 => "",
		),
		"CONVERT_CURRENCY" => "N",	// Показывать цены в одной валюте
		"DEPTH" => "",	// Максимальная отображаемая глубина разделов
		"DETAIL_URL" => "",	// URL, ведущий на страницу с содержимым элемента раздела
		"HIDE_NOT_AVAILABLE" => "N",	// Не отображать товары, которых нет на складах
		"IBLOCK_ID" => IBID_CATALOG,	// Инфоблок
		"IBLOCK_TYPE" => "1c_catalog",	// Тип инфоблока
		"LABEL_PROP_8" => "-",	// Свойство меток товара
		"LINE_ELEMENT_COUNT" => "3",	// Количество элементов, выводимых в одной строке
		"MESS_BTN_BUY" => "Купить",	// Текст кнопки "Купить"
		"MESS_BTN_DETAIL" => "Подробнее",	// Текст кнопки "Подробнее"
		"MESS_BTN_SUBSCRIBE" => "Подписаться",	// Текст кнопки "Уведомить о поступлении"
		"PAGE_ELEMENT_COUNT" => "10",	// Количество элементов на странице
		"PARTIAL_PRODUCT_PROPERTIES" => "N",	// Разрешить частично заполненные свойства
		"PRICE_CODE" => CATALOG_PRICE_CODES,
		"PRICE_VAT_INCLUDE" => "Y",	// Включать НДС в цену
		"PRODUCT_ID_VARIABLE" => "id",	// Название переменной, в которой передается код товара для покупки
		"PRODUCT_PROPS_VARIABLE" => "prop",	// Название переменной, в которой передаются характеристики товара
		"PRODUCT_QUANTITY_VARIABLE" => "",	// Название переменной, в которой передается количество товара
		"PRODUCT_SUBSCRIPTION" => "N",	// Разрешить оповещения для отсутствующих товаров
		"PROPERTY_CODE_8" => array(	// Свойства для отображения
			0 => "",
			1 => "",
		),
		"SECTION_CODE" => "",	// Код раздела
		"SECTION_ELEMENT_CODE" => "",	// Символьный код элемента, для которого будет выбран раздел
		"SECTION_ELEMENT_ID" => "",	// ID элемента, для которого будет выбран раздел
		"SECTION_ID" => "",	// ID раздела
		"SHOW_DISCOUNT_PERCENT" => "Y",	// Показывать процент скидки
		"SHOW_FROM_SECTION" => "N",	// Показывать товары из раздела
		"SHOW_IMAGE" => "Y",	// Показывать изображение
		"SHOW_NAME" => "Y",	// Показывать название
		"SHOW_OLD_PRICE" => "N",	// Показывать старую цену
		"SHOW_PRICE_COUNT" => "1",	// Выводить цены для количества
		"SHOW_PRODUCTS_".IBID_CATALOG => "Y",	// Показывать товары каталога
		"TEMPLATE_THEME" => "blue",	// Цветовая тема
		"USE_PRODUCT_QUANTITY" => "N",	// Разрешить указание количества товара
		"COMPONENT_TEMPLATE" => "viewed_products"
	),
	false
);?>
```
**Но есть нюанс.** Это справедливо, если Вы кастомизируете шаблон вывода товара, что бывает не так уж и редко.

Если не сообщить компоненту, что страница товара была посещена, то и списка посещенных страниц не будит. 
Логично предположить, что компонент посещенных страниц сам должен за этим следить, а вот нет, прийдется нам ему помочь.

Для того что бы сообщить битриксу о факте просмотра элемента нужно передать код сайта 
и код просмотренного товара в скрипт ```/bitrix/components/bitrix/catalog.element/ajax.php```.

Сделать это можно в файле script.js шаблона вывода товара или в любом подключаемом в нем js файле.

**Пример передачи информации о посещении страницы**

В шаблоне сохраним информацию о коде элемента и сайта, что бы получить ее в скрипте.

```php
<?// данные для обработки через JS?>
<div id="productJSData" data-json='{ "id": "<?=$arResult['ID']?>", "siteId": "<?=SITE_ID?>" }'></div>
<?// / данные для обработки через JS?>
```

В скрипте получим данные из разметки и сообщим Битриксу о просмотре товара
```js
$(document).ready(function() {
    // прокинем информацию о том, что элемент "просмотрен"
    var jsDataJSON = $("#productJSData").data('json');

    // запрос на добавление элемента в "просмотренные"
    $.ajax({
        url: '/bitrix/components/bitrix/catalog.element/ajax.php',
        method: 'POST',
        data: {
            AJAX: 'Y',
            SITE_ID: jsDataJSON.siteId,
            PRODUCT_ID: jsDataJSON.id,
            PARENT_ID: jsDataJSON.id
        },
        success: function(obj) {
            // some action on success
        },
        error: function(p1,p2,p3) {
            console.log('ERROR',p1,p2,p3);
        }
    });
});
```
