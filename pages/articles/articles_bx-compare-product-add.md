---
title: Статьи 1С Битрикс | Добавление товара в сравнение
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-compare-product-add.html
summary: false
toc: true
---

## Добавление товара в список сравнения

### Задача

Добавить товар в список сравнения.

### Решение

#### Шаблон компонента каталога

```php
<!-- кнопка добавления/удаления товара из сравнения -->
<a href="<?=SITE_TEMPLATE_PATH?>/ajax/addToCompare.php"
   data-prod-id="<?=$arItem['ID']?>"
   class="add-to-compare jsToggleCompare <?$APPLICATION->ShowProperty('COMPARE_BTN_CLASS_'.$arItem['ID']);?>"><?$APPLICATION->ShowProperty('COMPARE_BTN_TEXT_'.$arItem['ID']);?></a>
```

{% include note.html content="См. связанную статью [Пример работы с component_epilog.php](/articles_bx-component-epilog-example.html)" %}

#### JS для кнопки добавления/удаления в/из избранного

```php
var compareUrl;

function changeCompareList(action, id, $cntContainer) {
    $.ajax({
        url: compareUrl+'?action='+action+'&id='+id,
        method: 'POST',
        dataType: 'json',
        data: {
            id:id
        },
        success: function(response) {
            // раньше компонент возвращал не валидный json, приходилось парсить
            // response = response.replace(/\'/g, '"');
            // response = $.parseJSON(response);//parse JSON
            
            if (response.STATUS === "OK") {
                //alert('Товар добавлен в список сравнения');
            } else {
                alert('Ошибка добавления в список сравнения');
            }
            
            // изменение количества избранных товаров в информационной плашке
            if (response.COUNT !== '0') {
                $cntContainer.removeClass('hidden').html(response.COUNT);
            } else {
                $cntContainer.addClass('hidden').html(response.COUNT);
            }
        },
        error: function(request, status, error) {
            alert('Произошла ошибка:' + error);
        }

    });
}

function addToCompareList(id, $cntContainer) {
    changeCompareList('ADD_TO_COMPARE_LIST', id, $cntContainer);
}

function removeFromCompareList(id, $cntContainer) {
    changeCompareList('DELETE_FROM_COMPARE_LIST', id, $cntContainer);
}

$(document).ready(function() {
    $(document).on('click', '.jsToggleCompare', function(){
        var $this = $(this);
        var id = $this.attr('data-prod-id');
        var $cntContainer = $('.compare-cnt');

        compareUrl = $this.attr('href');

        console.log(compareUrl);
        
        if($this.hasClass('active')) {
            $this.removeClass('active').html('Добавить к сравнению');
            removeFromCompareList(id, $cntContainer);
        } else {
            $this.addClass('active').html('Удалить из сравнения');
            addToCompareList(id, $cntContainer);
        }

        return false;
    });
});
```

#### Обработчик добавления/удаления в/из избранного

```php
<?php include_once $_SERVER["DOCUMENT_ROOT"] . '/bitrix/modules/main/include/prolog_before.php';

/**
 *
 * Добавить
 * ?action=ADD_TO_COMPARE_LIST&id=96
 *
 * Удалить
 * ?action=DELETE_FROM_COMPARE_LIST&id=96
 *
 */

/** Чтобы по умолчанию режим ajax был включен **/
$_REQUEST["ajax_action"] = "Y";

$APPLICATION->IncludeComponent(
    "bitrix:catalog.compare.list",
    "",
    Array(
        "ACTION_VARIABLE" => "action",
        "AJAX_MODE" => "Y",
        "AJAX_OPTION_ADDITIONAL" => "",
        "AJAX_OPTION_HISTORY" => "N",
        "AJAX_OPTION_JUMP" => "N",
        "AJAX_OPTION_STYLE" => "Y",
        "COMPARE_URL" => "/catalog/compare/",
        "COMPONENT_TEMPLATE" => ".default",
        "DETAIL_URL" => "",
        "IBLOCK_ID" => IBID_CATALOG,
        "IBLOCK_TYPE" => "catalogs", //iblock_type
        "NAME" => 'CATALOG_COMPARE_LIST',
        "POSITION" => "top left",
        "POSITION_FIXED" => "Y",
        "PRODUCT_ID_VARIABLE" => "id"
    )
);
```
