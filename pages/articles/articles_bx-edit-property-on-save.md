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

Но в условиях нашей задачи речь идет о товарах и о значении типа цены товара. 
В момент создания и обновления элемента инфоблока данных о его цене еще нет и получить мы их в эти моменты не можем.
Поэтому нам нужно использовать события:

  * Для добавления [OnPriceAdd](https://dev.1c-bitrix.ru/api_help/catalog/events/onpriceadd.php)
  * Для создания [OnPriceUpdate](https://dev.1c-bitrix.ru/api_help/catalog/events/onpriceupdate.php)

#### Создание обработчиков событий

```php
$eventManager = \Bitrix\Main\EventManager::getInstance();

// CDec1C
$eventManager->addEventHandler("catalog", "OnBeforePriceAdd", ["CDec1C", "handlerOnBeforePriceAdd"]);
$eventManager->addEventHandler("iblock", "OnAfterIBlockElementUpdate", ["CDec1C", "handlerOnAfterIBlockElementUpdate"]);
```

#### Код функций обработчиков

```php
<?php
class CDec1C {
    /** 
     * Событие до добавления цены в товар
	    **/
	   public function handlerOnBeforePriceAdd($arFields) {
		      $arProductFields = CCatalogProduct::GetByIDEx($arFields['PRODUCT_ID']);
		
		      $arDop['price'] = $arFields['PRICE'];
		
		      self::_updateAutoPriceSort($arProductFields, $arDop);
	   }

	   /** 
	    * Событие после изменения элемента инфоблока
	    **/
	   public function handlerOnAfterIBlockElementUpdate($arFields) {
		      self::_updateAutoPriceSort($arFields);
		 	}
}
```

#### Код вспомогательной функции

```php
/** 
	 * Функция вычисления цены для сортировки
	 * И дату окончания акции
	 * */
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
		
		if(    $vUpakovke
			&& !in_array($arItem['IBLOCK_SECTION_ID'], CCommon::M_PRICE_CATS_ESC)) 
		{
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

            //CCommon::dp($arItem['PROPERTY_AKTSIYA_DEYSTVUET_DO_VALUE']);
            //CCommon::dp($dt);

			$actionDateEnd = $dt->format('d.m.Y H:i:s');

			if ($actionDateEnd) {
				//CCommon::dp($actionDateEnd);
				CIBlockElement::SetPropertyValues($arItem['ID'], IBID_CATALOG, $actionDateEnd, 'ACTION_DATE_END');
			}
		}
	}
```
