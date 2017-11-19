---
title: Добавление неограниченного количества сортировок в компонент catalog.section | Статьи CMS "1С Битрикс"
keywords: сортировка товаров битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-catalog-section-unlimited-sort.html
summary: false
toc: false
---

### Задача

Добавить дополнительные уровни сортировки в компонент вывода списка товаров.

### Решение

Решить эту задачу без кастомизации компонента не получится. 

Битрикс позволяет нам кастомизировать компонент двумя способами:

* Наследованием от нужного компонента (D7)
* Копированием и кастомизацией имеющегося кода

#### Кастомизация при помощи наследования компонента

[Документация](https://dev.1c-bitrix.ru/learning/course/?COURSE_ID=43&LESSON_ID=2028)

Так же как и в варианте с копированием нам нужно создать папку для своего компонента в своем пространстве имен.

В нашем примере создадим папку: ```local/components/yournamespase/catalog.section```

В созданой директории создадим файл ```class.php``` с таким содержимым:

```php
<?
if (!defined('B_PROLOG_INCLUDED') || B_PROLOG_INCLUDED !== true) die();

// подключаем класс компонента от которого хотим унаследовать наш компонент
CBitrixComponent::includeComponentClass("bitrix:catalog.section");

// расширяем класс компонента, пишем новые функции и/или переопределяем существующие
class DecCatalogSectionComponent extends \CatalogSectionComponent
{
	protected function getSort()
    {
        $sortFields = [];

        if (
            (
                $this->isIblockCatalog
                || (
                    $this->isMultiIblockMode()
                    || (!$this->isMultiIblockMode() && $this->offerIblockExist($this->arParams['IBLOCK_ID']))
                )
            )
            && $this->arParams['HIDE_NOT_AVAILABLE'] === 'L'
        )
        {
            $sortFields['CATALOG_AVAILABLE'] = 'desc,nulls';
        }

        if (is_array($this->arParams['ELEMENT_SORT_ARRAY']) && count($this->arParams['ELEMENT_SORT_ARRAY']) > 0) {

            $sortFields = array_merge($sortFields, $this->arParams['ELEMENT_SORT_ARRAY']);

        } else {

            if (!isset($sortFields[$this->arParams['ELEMENT_SORT_FIELD']]))
            {
                $sortFields[$this->arParams['ELEMENT_SORT_FIELD']] = $this->arParams['ELEMENT_SORT_ORDER'];
            }

            if (!isset($sortFields[$this->arParams['ELEMENT_SORT_FIELD2']]))
            {
                $sortFields[$this->arParams['ELEMENT_SORT_FIELD2']] = $this->arParams['ELEMENT_SORT_ORDER2'];
            }
        }

        return $sortFields;
    }
}
```

##### Вызов компонента

```php
<?
$intSectionID = $APPLICATION->IncludeComponent(
    "yournamespase:catalog.section",
    "",
    array( 
        // тут обычный массив параметров, с добавлением нужного нам ELEMENT_SORT_ARRAY
    ),
    $component
);
```

В подключении компонента нужно передать параметр **ELEMENT_SORT_ARRAY**, который жолжен быть массивом вида **["ИМЯ_ПОЛЯ_ИЛИ_СВОЙСТВА_ДЛЯ_СОРТИРОВКИ" => "НАПРАВЛЕНИЕ_СОРТИРОВКИ", ...]**

#### Кастомизация копированием

Для этого копируем компонент **bitrix:catalog.section** в свое пространство имен. Например так: ```local/components/yournamespase/catalog.section```.

И вносим изменения в файл class.php компонента. Добавляем функцию, которая переопределит родительскую функцию сортировки:

```php
<?
    protected function getSort()
    {
        $sortFields = [];

        if (
            (
                $this->isIblockCatalog
                || (
                    $this->isMultiIblockMode()
                    || (!$this->isMultiIblockMode() && $this->offerIblockExist($this->arParams['IBLOCK_ID']))
                )
            )
            && $this->arParams['HIDE_NOT_AVAILABLE'] === 'L'
        )
        {
            $sortFields['CATALOG_AVAILABLE'] = 'desc,nulls';
        }

        if (is_array($this->arParams['ELEMENT_SORT_ARRAY']) && count($this->arParams['ELEMENT_SORT_ARRAY']) > 0) {

            $sortFields = array_merge($sortFields, $this->arParams['ELEMENT_SORT_ARRAY']);

        } else {

            if (!isset($sortFields[$this->arParams['ELEMENT_SORT_FIELD']]))
            {
                $sortFields[$this->arParams['ELEMENT_SORT_FIELD']] = $this->arParams['ELEMENT_SORT_ORDER'];
            }

            if (!isset($sortFields[$this->arParams['ELEMENT_SORT_FIELD2']]))
            {
                $sortFields[$this->arParams['ELEMENT_SORT_FIELD2']] = $this->arParams['ELEMENT_SORT_ORDER2'];
            }
        }

        return $sortFields;
    }
?>
```
##### Вызов компонента

```php
<?
$intSectionID = $APPLICATION->IncludeComponent(
    "yournamespase:catalog.section",
    "",
    array( 
        // тут обычный массив параметров, с добавлением нужного нам ELEMENT_SORT_ARRAY
    ),
    $component
);
```

В подключении компонента нужно передать параметр **ELEMENT_SORT_ARRAY**, который жолжен быть массивом вида **["ИМЯ_ПОЛЯ_ИЛИ_СВОЙСТВА_ДЛЯ_СОРТИРОВКИ" => "НАПРАВЛЕНИЕ_СОРТИРОВКИ", ...]**
