---
title: Статьи CMS "1С Битрикс" | Поиск товаров по цепочке разделов, переиндексация поиска
keywords: поиск 1с битрикс, переиндексация поиска, найти товар по названию раздела
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-search-group-chain-in-tovar-and-reindex.html
summary: false
toc: false
---

### Задача

На сайте есть каталог товаров с многоуровневой вложенностью разделов. Требуется обеспечить нахождение товаров по запросам которые включают в себя названия разделов.

Пример: есть раздел "моющие средства" и товар "мыло". В обычном случае компонент поиска в Битрикс при запросе "мыло" найдет нужный товар, а при запросе "моющее средство мыло" не найдет. Нужно это исправить.

### Решение

Общая идея сводится к получению цепочки названий разделов, к которым принадлежит товар. Эту цепочку нужно сохранить в инфоблок товаров в отдельное поле, это поле добавить к списку участвующих в поиске. И переиндексировать поиск, что бы изменение данных товара попало в поисковый индекс.

Делать все эти действия можно либо на событие изменения товара, если у Вас ручное наполнение каталога, в этом случае нужно индексировать только изменения.

Если же у Вас проходит синхронизация с 1С, то описанные выше действия нужно выполнить после завершения синхронизации. О том, как корректно отловить событие окончания синхронизации с 1С можно узнать в статье [Умный фильтр, бизнес-логика сайта и перестроение фасетного индекса](/articles_bx-smart-filter-faset.html#установка-выполнения-деактивации-и-переиндексации-на-событие-завершения-обмена-с-1с)

#### Битрикс настройка

Сперва проведем настройки в админке битрикса. Добавим в инфоблок каталога поле "Цепочка разделов для поиска" с типом "строка", ставим галку "Участвует в поиске". В это поле мы будем сохранять цепоцку с названиями разделов к которым принадлежит товар.

#### PHP код 

Пример получения цепочки разделов по коду товара, обновление свойства и переиндексации поиска:

```php
<?php
class CSearchHelper {
    /**
     * Обновление цепочек принадлежности к разделам
     * @param int $iblockId
     * @param array $arFilter
     * @return bool|int|array
     */
    public static function productsGroupChainRefresh($iblockId, $arFilter) {
        if (!\Bitrix\Main\Loader::includeModule('iblock')
            || !\Bitrix\Main\Loader::includeModule('search')) {
            return false;
        }

        $arFilterFinal = [
            'IBLOCK_ID' => $iblockId,
            $arFilter,
        ];

        $dbEls = CIBlockElement::GetList([], $arFilterFinal, false, false, ['ID', 'NAME', 'IBLOCK_SECTION_ID']);

        $arSecTree = [];

        while ($arEl = $dbEls->GetNext()) {

            if($arEl["IBLOCK_SECTION_ID"] || !isset($arSecTree[$arEl["IBLOCK_SECTION_ID"]])) {

                $dbChain = CIBlockSection::GetNavChain(false, $arEl["IBLOCK_SECTION_ID"]);
                $arChain = [];
                while ($arGroup = $dbChain->GetNext()) {
                    $arChain[] = $arGroup['NAME'];
                }

                $arSecTree[$arEl["IBLOCK_SECTION_ID"]] = implode(', ', $arChain);

            }

            CIBlockElement::SetPropertyValuesEx($arEl['ID'], false, array('GROUP_CHAIN' => $arSecTree[$arEl["IBLOCK_SECTION_ID"]]));

        }

        //переиндексация поиска
        $max_execution_time = 20;
        $NS = [];

        do {
            $NS = CSearch::ReIndexAll(true, $max_execution_time, $NS);
        } while(is_array($NS));

        return $NS;

    }
}
?>
```

Функцию ```productsGroupChainRefresh``` нужно вызвать после завершения выгрузки, передав в нее параметр $iblockId - код информационного блока и $arFilter - фильтр для отбора элементов
