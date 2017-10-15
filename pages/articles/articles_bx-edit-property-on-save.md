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
  * Перед созданием элемента [OnAfterIBlockElementAdd](https://dev.1c-bitrix.ru/api_help/iblock/events/onafteriblockelementadd.php)
  * Перед изменением элемента [OnAfterIBlockElementUpdate](https://dev.1c-bitrix.ru/api_help/iblock/events/onafteriblockelementupdate.php)

Но в условиях нашей задачи речь идет о товарах и о значении типа цены товара. 
В момент создания и обновления элемента инфоблока данных о его цене еще нет и получить мы их в эти моменты не можем.
Поэтому нам нужно использовать события:

  * Для добавления [OnPriceAdd](https://dev.1c-bitrix.ru/api_help/catalog/events/onpriceadd.php)
  * Для создания [OnPriceUpdate](https://dev.1c-bitrix.ru/api_help/catalog/events/onpriceupdate.php)
  
{% include note.html content="Эти события выполняются ДЛЯ КАЖДОГО ЗАПОЛНЕННОГО типа цен на товаре, т.е. если у Вас 10 типов цен и 3 из них имеют не пустое значение, то событие выполнится 3 раза" %}

{% include note.html content="Интересная деталь: при изменении товара, если тип цены был пуст и его поставить в 0, то событие OnPriceUpdate НЕ выполнится, а выполнится OnPriceAdd, если тип цены уже равен 0 и сохранить товар с ним не меняя значения, то событие OnPriceUpdate выполнится, так же оно НЕ выполнится и при изменении значения с 0 на 'пусто', есть событие OnPriceDelete, но его перехватить не удалось" %}

#### Создание обработчиков событий

```php
<?
$eventManager = \Bitrix\Main\EventManager::getInstance();

// CDec1C
$eventManager->addEventHandler("catalog", "OnPriceAdd", ["CDec1C", "handlerOnPriceAdd"]);
$eventManager->addEventHandler("catalog", "OnPriceUpdate", ["CDec1C", "handlerOnPriceUpdate"]);
$eventManager->addEventHandler("iblock", "OnAfterIBlockElementUpdate", ["CDec1C", "handlerOnAfterIBlockElementUpdate"]);
?>
```

#### Код функций обработчиков

```php
<?php
class CDec1C {
    /** 
	 * Событие после изменения элемента инфоблока
	 * */
	public function handlerOnAfterIBlockElementUpdate($arFields) {
		self::_updateAutoPriceSort($arFields);
	}

    /**
     * Событие после добавления цены элемента инфоблока
     * */
    public function handlerOnPriceAdd($id, $arFields) {
        // обновляем только при изменении основной цены
        if ($arFields["CATALOG_GROUP_ID"] !== PRICE_ROZNICA_ID) {
            return;
        }

        $arProductFields = CCatalogProduct::GetByIDEx($arFields['PRODUCT_ID']);

        $arDop['price'] = $arFields['PRICE'];

        self::_updateAutoPriceSort($arProductFields, $arDop);

    }

    /**
     * Событие после изменения цены элемента инфоблока
     * */
    public function handlerOnPriceUpdate($id, $arFields) {

        // обновляем только при изменении основной цены
        if ($arFields["CATALOG_GROUP_ID"] !== PRICE_ROZNICA_ID) {
            return;
        }

        $arProductFields = CCatalogProduct::GetByIDEx($arFields['PRODUCT_ID']);

        $arDop['price'] = $arFields['PRICE'];

        self::_updateAutoPriceSort($arProductFields, $arDop);

    }
    
    /** 
     * Функция вычисления цены для сортировки
     * И дату окончания акции
     **/
    static private function _updateAutoPriceSort($arFields, $arDop = []) {

        // Свойство "Дата окончания акции"
        $propCode = 'ACTION_DATE_END';

        if (!self::_checkPropExists($propCode)) {
	    $arFields = Array(
                "NAME" => "Дата окончания акции",
                "ACTIVE" => "Y",
                "SORT" => "6000",
                "CODE" => $propCode,
                "PROPERTY_TYPE" => 'S',
                "USER_TYPE" => 'DateTime',
                "IBLOCK_ID" => IBID_CATALOG,
            );

            $ibp = new CIBlockProperty;
            $PropID = $ibp->Add($arFields);
        }

        // Свойство "Цена для сортировки (авто)"
        $propCode = 'AUTO_PRICE_SORT';

        if (!self::_checkPropExists($propCode)) {
            $arFields = Array(
                "NAME" => "Цена для сортировки (авто)",
                "ACTIVE" => "Y",
                "SORT" => "6000",
                "CODE" => $propCode,
                "PROPERTY_TYPE" => 'N',
                "IBLOCK_ID" => IBID_CATALOG,
            );

            $ibp = new CIBlockProperty;
            $PropID = $ibp->Add($arFields);
        }

        if(!$arFields['ID']) {
            return;
        }

        // получим элемент инфоблока
        $arSelect = [
            'IBLOCK_ID',
            'ID',
	    'IBLOCK_SECTION_ID',
	    'CATALOG_GROUP_'.PRICE_ROZNICA_ID,
	    'PROPERTY_V_ODNOY_UPAKOVKE_M',
	    'PROPERTY_AUTO_PRICE_SORT',
	    'PROPERTY_AKTSIYA_DEYSTVUET_DO',
	    'PROPERTY_ACTION_DATE_END',
        ];

        $arOrder = [];

        $arWhere = [
            'IBLOCK_ID' => IBID_CATALOG,
            'ID' => $arFields['ID'],
        ];

        $dbItem = CIBlockElement::getList($arOrder, $arWhere, false, false, $arSelect);
        $arItem = $dbItem->getNext();

        if( CCommon::CATALOG_ID !== (int)$arItem['IBLOCK_ID'] ) {
            return;
        }

        // Работа со свойствами стоимости

        $vUpakovke = $arItem['PROPERTY_V_ODNOY_UPAKOVKE_M_VALUE'];
        $price = floatVal($arItem['CATALOG_PRICE_'.PRICE_ROZNICA_ID]);
		
        if(isset($arDop['price']) && $arDop['price'] > 0) {
            $price = $arDop['price'];
        }

        $sortPriceBefore = floatVal($arItem['PROPERTY_AUTO_PRICE_SORT_VALUE']);
        $sortPriceAfter = $price;

        if ( $vUpakovke
            && !in_array($arItem['IBLOCK_SECTION_ID'], CCommon::M_PRICE_CATS_ESC)
        ) {
            $vUpak = floatVal(str_replace(',', '.', $vUpakovke));
            $sortPriceAfter = round($price / $vUpak);
        }

        if ($sortPriceAfter !== $sortPriceBefore) {
            CIBlockElement::SetPropertyValues($arItem['ID'], IBID_CATALOG, $sortPriceAfter, 'AUTO_PRICE_SORT');
        }

        // Работа со свойствами даты акции
        if ($arItem['PROPERTY_AKTSIYA_DEYSTVUET_DO_VALUE']) {

            $dt = DateTime::createFromFormat('d.m.Y G:i:s', $arItem['PROPERTY_AKTSIYA_DEYSTVUET_DO_VALUE']);

            if(!$dt) {
                $dt = DateTime::createFromFormat('Y-m-d\TH:i:s', $arItem['PROPERTY_AKTSIYA_DEYSTVUET_DO_VALUE']);
            }
	
	    $actionDateEnd = $dt->format('d.m.Y H:i:s');
        
	    if ($actionDateEnd) {
                CIBlockElement::SetPropertyValues($arItem['ID'], IBID_CATALOG, $actionDateEnd, 'ACTION_DATE_END');
            }
        }
    }
}
```

