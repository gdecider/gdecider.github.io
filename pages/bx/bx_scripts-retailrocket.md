---
title: Проект | Установка скриптов | Retail Rocket
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_scripts-retailrocket.html
summary: false
toc: false
---

## Установка скрипта Retail Rocket

[Инструкция по установке на оф. сайте](https://studioratio.ru/retailrocket/)

### 1. Зарегистрироваться в системе

[Retail Rocket](https://my.retailrocket.ru/PartnerAccount/Create)

### 2. Установить модуль

[Маркетплейс модуль Retail Rocket](http://marketplace.1c-bitrix.ru/solutions/quetzal.retailrocket/)

В процессе установки ввести авторизационные данные.

### 3. Указать селекторы кнопок добавления товара в корзину

В процессе установки модуля в поле "Данные кнопок" указать селекторы кнопок добавления товара в корзину, каждый новый селектор пишется в новой строке.

Так же это можно сделать после установки в настройках модуля: `Сервисы -> Retail Rocket -> Настройки модуля`

Формат: `<селектор>:<атрибут с идентификатором товара>`

Пример:

```
a.js_buy_btn;data-id
.doAddToCart;data-id
.doOpenFormOneClickBuy;data-id
```

Велика вероятность, что это решение не заработает и нужно воспользоваться альтернативным вариантом - прописать событие на кнопках в разметке:

* Для категорий в файл `<SITE_TEMPLATE_PATH>/components/bitrix/catalog.section/.default/template.php`

* Для товаров в файл `<SITE_TEMPLATE_PATH>/components/bitrix/catalog.element/.default/template.php`

Пример:

```html
<a onmousedown="try { rrApi.addToBasket(<?=$arResult["ID"]?>) } catch(e) {}">В корзину</a>
```

### 4. Указать ссылку на YML файл выгрузки товаров

Для работы сервиса Retail Rocket необходима выгрузка информации о ваших товарах в формате YML. Сгенерировать файл можно средствами 1с-Битрикс, для этого воспользуйтесь профилем Yandex в разделе Магазин → Экспорт данных и сохраните ссылку на полученный файл.

Переходим на страницу настроек модуля в административной части сайта Сервисы → Retail Rocket, устанавливаем ссылку на YML и корзину. 

### 5. Установить компонент Retail Rocket на страницу карточки товара

В файл `<SITE_TEMPLATE_PATH>/optovikk/components/bitrix/catalog/catalog/element.php` добавить код:

```php
<?$APPLICATION->IncludeComponent(
    "quetzal:tracking.product", "",
    Array(
        "CARD_PRODUCT_PARAM" => $ElementID
    ), $component
);?>
```

### 6. Установить компонент Retail Rocket на страницу категории товаров

В файл `<SITE_TEMPLATE_PATH>/optovikk/components/bitrix/catalog/catalog/section.php` добавить код:

```php
<?$APPLICATION->IncludeComponent(
    "quetzal:tracking.category", "",
    Array(
        "CATEGORY_PAGE_PARAM" => $intSectionID
    ), $component
);?>
```

### 7. Установить компонент Retail Rocket на страницу завершения оформления заказа

В файл `<SITE_TEMPLATE_PATH>/components/bitrix/sale.order.ajax/make_order/confirm.php` добавить код:

```php
<?$APPLICATION->IncludeComponent(
    "quetzal:tracking.order", "",
    Array(
        "ORDER_PARAM_TRANSACTION" => $arResult["ORDER_ID"]
    ), $component
);?>
```

### 8. Добавить перехват email адресов

На события отправки форм, в которых есть поля для заполнения email, добавить перехват:

```js
(window["rrApiOnReady"] = window["rrApiOnReady"] || []).push(function() { rrApi.setEmail(<email>);	});
```

Например при оформлении заказа `<SITE_TEMPLATE_PATH/components/bitrix/sale.order.ajax/make_order/order_ajax.js>`

```js
if (action == 'saveOrderAjax')
{
	form = BX('bx-soa-order-form');
	if (form) {
		form.querySelector('input[type=hidden][name=sessid]').value = BX.bitrix_sessid();
		// начало кода перехвата email
		var $form = $('#bx-soa-order-form');
		var email = $form.find('[name="ORDER_PROP_2"]').val();
		
		(window["rrApiOnReady"] = window["rrApiOnReady"] || []).push(function() { rrApi.setEmail(email);	});
		// окончание кода перехвата email
	}

	BX.ajax.submit(BX('bx-soa-order-form'), BX.proxy(this.saveOrder, this));
}
```