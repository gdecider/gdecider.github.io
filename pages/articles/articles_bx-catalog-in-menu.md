---
title: Статьи 1С Битрикс | Вывод категорий каталога в меню
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-catalog-in-menu.html
summary: false
toc: false
---

## Вывод категорий каталога в меню

### Задача

Вывести пункты меню, с возможностью их редактирования стандартными средствами административной панели Битрикс, после этих пунктов вывести спикок категорий из инфоблока каталога.

### Решение.

#### PHP

**1) В нужном месте страницы размещаем компонент вывода меню**

```php
<?$APPLICATION->IncludeComponent("bitrix:menu", "menuSideCatalog", Array(
    "ALLOW_MULTI_SELECT" => "N",	// Разрешить несколько активных пунктов одновременно
    "CHILD_MENU_TYPE" => "mside_catalog",	// Тип меню для остальных уровней
    "DELAY" => "N",	// Откладывать выполнение шаблона меню
    "MAX_LEVEL" => "1",	// Уровень вложенности меню
    "MENU_CACHE_GET_VARS" => array(	// Значимые переменные запроса
        0 => "",
    ),
    "MENU_CACHE_TIME" => "3600",	// Время кеширования (сек.)
    "MENU_CACHE_TYPE" => "N",	// Тип кеширования
    "MENU_CACHE_USE_GROUPS" => "Y",	// Учитывать права доступа
    "ROOT_MENU_TYPE" => "mside_catalog",	// Тип меню для первого уровня
    "USE_EXT" => "Y",	// Подключать файлы с именами вида .тип_меню.menu_ext.php
),
    false
);?>
```

Проверем разрешение на подключение файлов _ext ```"USE_EXT" => "Y"```

**2) Создаем файл ```.mside_catalog.menu_ext.php``` в корне сайта (или в группе в которой он должен добавляться к нашему меню)**

```php
<? if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();

global $APPLICATION;
$aMenuLinksExt = array();

if(CModule::IncludeModule('iblock'))
{
    $arFilter = array(
        "TYPE" => "catalogs",
        "SITE_ID" => SITE_ID,
    );

    $dbIBlock = CIBlock::GetList(array('SORT' => 'ASC', 'ID' => 'ASC'), $arFilter);
    $dbIBlock = new CIBlockResult($dbIBlock);

    if ($arIBlock = $dbIBlock->GetNext())
    {
        if(defined("BX_COMP_MANAGED_CACHE"))
            $GLOBALS["CACHE_MANAGER"]->RegisterTag("iblock_id_".$arIBlock["ID"]);

        if($arIBlock["ACTIVE"] == "Y")
        {
            $aMenuLinksExt = $APPLICATION->IncludeComponent("bitrix:menu.sections", "", array(
                "IS_SEF" => "Y",
                "SEF_BASE_URL" => "",
                "SECTION_PAGE_URL" => $arIBlock['SECTION_PAGE_URL'],
                "DETAIL_PAGE_URL" => $arIBlock['DETAIL_PAGE_URL'],
                "IBLOCK_TYPE" => $arIBlock['IBLOCK_TYPE_ID'],
                "IBLOCK_ID" => $arIBlock['ID'],
                "DEPTH_LEVEL" => "1",
                "CACHE_TYPE" => "N",
            ), false, Array('HIDE_ICONS' => 'Y'));
        }
    }

    if(defined("BX_COMP_MANAGED_CACHE"))
        $GLOBALS["CACHE_MANAGER"]->RegisterTag("iblock_id_new");
}

$aMenuLinks = array_merge($aMenuLinks, $aMenuLinksExt);
?>
```

**3) Пример шаблона компонента ```/local/templates/mainTpl/components/bitrix/menu/menuSideCatalog/template.php```**

```php
<?if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();?>

<?if (!empty($arResult)):?>
    <div class="catalog__title">каталог</div>
    <ul class="catalog__list" <?=(CSite::InDir('/index.php')) ? 'style="display:block;"' : ''?>>
        <?foreach($arResult as $arItem):?>
            <li class="catalog__list-item <?echo $arItem['PARAMS']['CLASS'] ? $arItem['PARAMS']['CLASS'] : ''?>">
                <a href="<?=$arItem["LINK"]?>"><?=$arItem["TEXT"]?></a></li>
        <?endforeach;?>
    </ul>
<?endif?>
```