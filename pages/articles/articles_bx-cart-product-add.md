---
title: Статьи 1С Битрикс | Добавление товара в корзину
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-cart-product-add.html
summary: false
toc: true
---

## Добавление товара в корзину

### Задача.

Добавить товар в корзину.

### Решение

#### Шаблон компонента каталога

```php
<!-- количество товара -->
<div class="counter jsIncrementer">
    <input type="text" size="3" value="1" class="counter_input">
    <div class="controls">
        <a href="javascript:void(0);" class="plus">+</a>
        <a href="javascript:void(0);" class="minus">&ndash;</a>
    </div>
</div>
<!-- кнопка добавления -->
<button class="add-to-cart jsAddToCart <?$APPLICATION->ShowProperty('CART_BTN_CLASS_'.$arItem['ID']);?>"
        data-prod-id="<?=$arItem['ID']?>"
        data-url="<?=SITE_TEMPLATE_PATH?>/ajax/addToCart.php">
            <?$APPLICATION->ShowProperty('CART_BTN_TEXT_'.$arItem['ID']);?>
</button>
```

{% include note.html content="См. связанную статью [Пример работы с component_epilog.php](/articles_bx-component-epilog-example.html)" %}

#### JS для работы кнопок изменения количества товара и запроса на добавление в корзину

```js
$(function () {

    $('.jsIncrementer .plus').on('click', function(){
        var $this = $(this);
        var $container = $this.closest('.jsIncrementer');
        var $input = $container.find('.counter_input');

        var curVal = parseInt($input.val());

        var newVal = curVal + 1;

        if(newVal <= 0) {
            newVal = 1;
        }

        $input.val(newVal);

        return false;
    });

    $('.jsIncrementer .minus').on('click', function(){
        var $this = $(this);
        var $container = $this.closest('.jsIncrementer');
        var $input = $container.find('.counter_input');

        var curVal = parseInt($input.val());

        var newVal = curVal - 1;

        if(newVal <= 0) {
            newVal = 1;
        }

        $input.val(newVal);

        return false;
    });

    $('.jsAddToCart').on('click', function(){
        var $this = $(this);
        var prodId = parseInt($this.data('prod-id'));
        var cartAddUrl = $this.data('url');
        var $item = $this.closest('.jsItem');
        var cnt = $item.find('.jsIncrementer .counter_input').val();

        console.log('prodId', prodId);

        $.ajax({
            url:cartAddUrl,
            method:'post',
            dataType: 'json',
            data: {
                'cnt': cnt,
                'prod-id': prodId,
            },
            success: function(obj) {
                if (obj.hasError) {
                    alert('Ошибка добавления товара в корзину');
                    return false;
                }

                $this.html('в корзине').addClass('active');
                console.log(obj);
            },
            error: function(p1, p2, p3) {
                console.log(p1, p2, p3);
            }
        });

        return false;
    });
});
```

#### Обработчик добавления в корзину

{% include note.html content="Используем модуль local.common, в нем есть ф-я в которую обернуты методы АПИ Битрикса для добавления товара в корзину" %}

```php
<?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/prolog_before.php");?>
<?php
use \Local\Common\Cart;
use Bitrix\Main\Application;

$arResult = array(
    'hasError' => true,
    'msg' => "Ошибка отправки",
);

$request = Application::getInstance()->getContext()->getRequest();

if (!$request->isPost()) {
    echo json_encode($arResult);
    die();
}

$cnt = intval(htmlspecialcharsEx($request->getPost('cnt')));
$prodId = intval(htmlspecialcharsEx($request->getPost('prod-id')));

if ($cnt <= 0 && $prodId <= 0) {
    $arResult['msg'] = "Ошибка отправки 2";
    echo json_encode($arResult);
    die();
}

$item = Cart::add($prodId, $cnt);

if(!$item) {
    $arResult['msg'] = "Ошибка отправки 3";
    echo json_encode($arResult);
    die();
}

$arResult = array(
    'hasError' => false,
    'msg' => "Товар добавлен в корзину",
);

echo json_encode($arResult);
die();

?>
```
