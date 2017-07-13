---
title: Статьи 1С Битрикс | Массовое изменение сортировки на товарах в группе по флагу
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-set-sort-on-element-by-group.html
summary: false
toc: true
---

## Массовое изменение сортировки на товарах в группе по флагу

### Задача

Изменить сортировку элементов в группе. Выполнить это действие при условии установки флага "Изменить сортировку товаров",
само значение свойства сортировки для товаров взять у их родительской группы.

### Решение

#### Битрикс

Добавить пользовательское свойство раздела:
  
  * Тип данных: Да/Нет 
  * Код поля: UF_DO_RESORT 

#### Добавление обработчика события

```php
<?php
$eventManager = \Bitrix\Main\EventManager::getInstance();

// CDecIBEvent
$eventManager->addEventHandler("iblock", "OnBeforeIBlockSectionUpdate", ["CDecIBEvent", "OnBeforeIBlockSectionUpdateHandler"]);
?>
```

Код функции обработчика

```php
<?php
class CDecIBEvent {
	/**
	 * на обновление категории
	 * */
    public function OnBeforeIBlockSectionUpdateHandler(&$arFields) {

        if ( !isset($arFields['UF_DO_RESORT']) || (int)$arFields['UF_DO_RESORT'] <= 0 ) {
            return;
        }

        // получим все товары в категории и проставим им сортировку
        $arOrder = array('ID'=>'ASC');

        $arFilter = array(
            'IBLOCK_ID' => $arFields['IBLOCK_ID'],
            'SECTION_ID' => $arFields['ID'],
            'INCLUDE_SUBSECTIONS' => 'Y',
        );

        $rsItems = CIBlockElement::GetList($arOrder, $arFilter);

        $i = 0;
        while($arItem = $rsItems->Fetch()) {
            $i++;

            $el = new CIBlockElement();
            $el->Update($arItem["ID"], array('SORT' => $arFields['SORT']));
        }

        // сбросим флаг пересчета сортировки
        $arFields['UF_DO_RESORT'] = 0;
    }
}
```
