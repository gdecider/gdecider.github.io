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

Решить эту задачу без кастомизации компонента не получится. Для этого копируем компонент **bitrix:catalog.section** в свое пространство имен. Например так: ```local/components/yournamespase/catalog.section```.

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

В подключении компонента нужно передать параметр **ELEMENT_SORT_ARRAY**, который жолжен быть массивом вида **["ИМЯ_ПОЛЯ_ИЛИ_СВОЙСТВА_ДЛЯ_СОРТИРОВКИ" => "НАПРАВЛЕНИЕ_СОРТИРОВКИ", ...]**
