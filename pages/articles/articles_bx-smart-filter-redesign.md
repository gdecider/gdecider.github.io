---
title: Статьи 1С Битрикс | Смарт-фильтр редизайн
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx-smart-filter-redesign.html
summary: false
toc: true
---

## Смарт-фильтр редизайн

### Задача

Применить к умному фильтру битрикса свою верстку.

### Решение

#### Описание принципов работы

Стандартный шаблон компонента catalog.smart.filter очень тесно завязан на JS и имеющейся разметке. Ниже будет представлен минимальный код, который обеспечит нам корректную работу умного фильтра и даст возможность писать свою разметку. Но наша разметка, к сожалению, так же будет тесно связана с javascript кодом, плюсом является применение jQuery, в котором проще разобраться.

Главная задача нашего кода - получить правельно сформированную ссылку умного фильтра. На основе этой ссылки при загрузке страницы компонент отфильтрует нам корректное количество элементов каталога.

#### Пример файла template.php

```php
<?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
/** @var array $arParams */
/** @var array $arResult */
/** @global CMain $APPLICATION */
/** @global CUser $USER */
/** @global CDatabase $DB */
/** @var CBitrixComponentTemplate $this */
/** @var string $templateName */
/** @var string $templateFile */
/** @var string $templateFolder */
/** @var string $componentPath */
/** @var CBitrixComponent $component */
$this->setFrameMode(true);

$this->addExternalJs($templateFolder . '/js/partSmartFilter.js');
?>

<div class="catalog-filter">
    <div class="cb-outer">
        <input type="checkbox" id="cd_1" class="c-checkbox">
        <label for="cd_1" class="c-checkbox__label">Акции</label>
    </div>
    <div class="cb-outer">
        <input type="checkbox" id="cd_2" class="c-checkbox">
        <label for="cd_2" class="c-checkbox__label">Товары партнёров</label>
    </div>

    <form name="<?echo $arResult["FILTER_NAME"]."_form"?>" action="<?echo $arResult["FORM_ACTION"]?>" method="get" class="smartfilter">
        <?foreach($arResult["HIDDEN"] as $arItem):?>
            <input type="hidden" name="<?echo $arItem["CONTROL_NAME"]?>" id="<?echo $arItem["CONTROL_ID"]?>" value="<?echo $arItem["HTML_VALUE"]?>" />
        <?endforeach;?>

        <? // PRICES
        foreach($arResult["ITEMS"] as $key=>$arItem) :

            $key = $arItem["ENCODED_ID"];

            if(isset($arItem["PRICE"])):
                if ($arItem["VALUES"]["MAX"]["VALUE"] - $arItem["VALUES"]["MIN"]["VALUE"] <= 0)
                    continue;

                $step_num = 4;
                $step = ($arItem["VALUES"]["MAX"]["VALUE"] - $arItem["VALUES"]["MIN"]["VALUE"]) / $step_num;
                $prices = array();
                if (Bitrix\Main\Loader::includeModule("currency"))
                {
                    for ($i = 0; $i < $step_num; $i++)
                    {
                        $prices[$i] = CCurrencyLang::CurrencyFormat($arItem["VALUES"]["MIN"]["VALUE"] + $step*$i, $arItem["VALUES"]["MIN"]["CURRENCY"], false);
                    }
                    $prices[$step_num] = CCurrencyLang::CurrencyFormat($arItem["VALUES"]["MAX"]["VALUE"], $arItem["VALUES"]["MAX"]["CURRENCY"], false);
                }
                else
                {
                    $precision = $arItem["DECIMALS"]? $arItem["DECIMALS"]: 0;
                    for ($i = 0; $i < $step_num; $i++)
                    {
                        $prices[$i] = number_format($arItem["VALUES"]["MIN"]["VALUE"] + $step*$i, $precision, ".", "");
                    }
                    $prices[$step_num] = number_format($arItem["VALUES"]["MAX"]["VALUE"], $precision, ".", "");
                }?>

                <div class="catalog-filter-section">
                    <div class="catalog-filter-section__title open">Цена</div>
                    <div class="param-list-wrp small-padd">

                        <div class="range-slider-inputs-wrp">
                            <input type="text"
                                   class="minCost"
                                   value="<?echo $arItem["VALUES"]["MIN"]["HTML_VALUE"] ? $arItem["VALUES"]["MIN"]["HTML_VALUE"] : $arItem["VALUES"]["MIN"]["VALUE"]?>"
                                   name="<?echo $arItem["VALUES"]["MIN"]["CONTROL_NAME"]?>"
                                   id="<?echo $arItem["VALUES"]["MIN"]["CONTROL_ID"]?>"
                                   data-start-value="<?=$arItem["VALUES"]["MIN"]["VALUE"]?>"
                            />
                            <span>-</span>
                            <input type="text"
                                   class="maxCost"
                                   name="<?echo $arItem["VALUES"]["MAX"]["CONTROL_NAME"]?>"
                                   id="<?echo $arItem["VALUES"]["MAX"]["CONTROL_ID"]?>"
                                   value="<?echo $arItem["VALUES"]["MAX"]["HTML_VALUE"] ? $arItem["VALUES"]["MAX"]["HTML_VALUE"] : $arItem["VALUES"]["MAX"]["VALUE"]?>"
                                   data-start-value="<?=$arItem["VALUES"]["MAX"]["VALUE"]?>"
                            />

                        </div>

                        <div id="range-slider"></div>

                    </div>
                </div>
            <? endif; ?>
        <? endforeach; ?>



        <?//not prices
        foreach($arResult["ITEMS"] as $key=>$arItem) :
            if ( empty($arItem["VALUES"])
                || isset($arItem["PRICE"])
            ) {
                continue;
            }

            if ( $arItem["DISPLAY_TYPE"] == "A"
                && ($arItem["VALUES"]["MAX"]["VALUE"] - $arItem["VALUES"]["MIN"]["VALUE"] <= 0)
            ) {
                continue;
            }
            ?>

            <div class="catalog-filter-section">
                <div class="catalog-filter-section__title <?=($arItem["DISPLAY_EXPANDED"] == "Y") ? 'open' : ''?>"><?=$arItem["NAME"] ?></div>

                <?
                $arCur = current($arItem["VALUES"]);
                switch ($arItem["DISPLAY_TYPE"]) :
                    case "A"://NUMBERS_WITH_SLIDER
                    case "B"://NUMBERS
                    case "G"://CHECKBOXES_WITH_PICTURES
                    case "H"://CHECKBOXES_WITH_PICTURES_AND_LABELS
                    case "P"://DROPDOWN
                    case "R"://DROPDOWN_WITH_PICTURES_AND_LABELS
                    case "K"://RADIO_BUTTONS
                    case "U"://CALENDAR
                        echo 'Only checkbox supported';
                    break;
                    default: // checkboxes?>
                        <div class="param-list-wrp">
                            <? foreach($arItem["VALUES"] as $val => $ar): ?>
                            <div class="cb-outer">
                                <input type="checkbox"
                                       name="<? echo $ar["CONTROL_NAME"] ?>"
                                       id="<? echo $ar["CONTROL_ID"] ?>"
                                       class="c-checkbox jsGetItemsCnt"
                                       value="<? echo $ar["HTML_VALUE"] ?>"
                                    <? echo $ar["CHECKED"]? 'checked="checked"': '' ?> >
                                <label for="<? echo $ar["CONTROL_ID"] ?>" class="c-checkbox__label"><?=$ar["VALUE"];?></label>
                                <? if ($arParams["DISPLAY_ELEMENT_COUNT"] !== "N" && isset($ar["ELEMENT_COUNT"])):?>&nbsp;
                                    (<span data-role="count_<?=$ar["CONTROL_ID"]?>"><? echo $ar["ELEMENT_COUNT"]; ?></span>)
                                <?endif;?>
                            </div>
                            <?endforeach;?>

                            <? if (count($arItem["VALUES"]) > 10 ): ?>
                            <span class="show-all">Показать все</span>
                            <? endif; ?>

                        </div> <!-- /.param-list-wrp -->
                <?endswitch;?>
            </div>

            <? endforeach; ?>
        <a type="submit" class="button reset-filter jsFilterApply" href="<?=$arResult["SEF_SET_FILTER_URL"]?>">Применить</a>
        <a class="button reset-filter jsFilterReset" href="<?=$arResult["SEF_DEL_FILTER_URL"]?>">Сбросить фильтр</a>

    </form>

</div> <!-- /.catalog-filter -->
```

#### Пример файла partSmartFilter.js

Для создания ползунка используется [https://jqueryui.com/slider/](https://jqueryui.com/slider/)

```js
/**
 * AJAX запрос на получение данных об отборе фильтром
 * @type {string}
 */
function getFilterLinks($this) {
    var $form = $this.closest('form');
    var $btnApply = $form.find('.jsFilterApply');
    var reqData;

    reqData = $form.serializeArray();
    reqData.push({'name':'ajax', 'value':'y'});

    $.ajax({
        url: location.protocol + '//' + location.host + location.pathname,
        method: 'POST',
        type: 'POST',
        data: reqData,
        dataType:'text',
        success: function(obj) {
            obj = obj.replace(/\'/g, "\"");
            obj = JSON.parse(obj);

            // количество отобранных элементов
            //obj.ELEMENT_COUNT
            // ссылка для применения фильтра
            //obj.SEF_SET_FILTER_URL

            $btnApply.attr("href", obj.SEF_SET_FILTER_URL);
            $form.attr("action", obj.SEF_SET_FILTER_URL);
        },
        error: function(p1, p2, p3) {
            console.log('error');
            console.log(p1);
            console.log(p2);
            console.log(p3);
        }
    });
}

$(function() {

    // Элемент выбора диапазона. Поля ввода значений 
    var $inputMin = $('input.minCost').first();
    var $inputMax = $('input.maxCost').first();

    // Элемент выбора диапазона. минимальное значение
    var priceMin = parseInt($inputMin.data('start-value'));
    // Элемент выбора диапазона. текущее нижнее значение фильтра
    var priceMinCurrent = parseInt($inputMin.val());

    // Элемент выбора диапазона. максимальное значение
    var priceMax = parseInt($inputMax.data('start-value'));
    // Элемент выбора диапазона. текущее верхнее значение фильтра
    var priceMaxCurrent = parseInt($inputMax.val());

    // Элемент выбора диапазона. Сам ползунок
    var $rangeSlider = $("#range-slider");
    
    // Сброс настроек предыдущей инициализации
    $rangeSlider.slider('destroy');

    // Элемент выбора диапазона. Работа ползунка
    $rangeSlider.slider({ 
        min: priceMin,
        max: priceMax,
        values: [priceMinCurrent,priceMaxCurrent],
        range: true,
        stop: function(event, ui) {
            $inputMin.val($rangeSlider.slider("values",0));
            $inputMax.val($rangeSlider.slider("values",1));

            var $this = $(this);
            getFilterLinks($this);
        },
        slide: function(event, ui){
            $inputMin.val($rangeSlider.slider("values",0));
            $inputMax.val($rangeSlider.slider("values",1));
        }
    });
    
    // Синхронизация ползунка при изменении минимального значения в инпуте
    $inputMin.change(function(){
        var value1=$inputMin.val();
        var value2=$inputMax.val();

        if(parseInt(value1) > parseInt(value2)){
            value1 = value2;
            $inputMin.val(value1);
        }
        $rangeSlider.slider("values",0,value1);

        var $this = $(this);
        getFilterLinks($this);

        console.log('ch min');
    });

    // Синхронизация ползунка при изменении максимального значения в инпуте
    $inputMax.change(function(){
        var value1=$inputMin.val();
        var value2=$inputMax.val();

        if (value2 > priceMax) { value2 = priceMax; $inputMax.val(priceMax)}

        if(parseInt(value1) > parseInt(value2)){
            value2 = value1;
            $inputMax.val(value2);
        }
        $rangeSlider.slider("values",1,value2);

        var $this = $(this);
        getFilterLinks($this);
        console.log('ch max');
    });

    // обновление ссылки фильтра при выборе галок
    $('.jsGetItemsCnt').on('change', function () {
        var $this = $(this);
        getFilterLinks($this);
    });
});
```

#### Подключение компонента фильтра

Подключение компонента фильтра обычно делается в файле section.php комплексного компонента каталога.

```php
<?
if (CModule::IncludeModule("iblock")) {
    $arFilter = array (
        "ACTIVE" => "Y",
        "GLOBAL_ACTIVE" => "Y",
        "IBLOCK_ID" => $arParams["IBLOCK_ID"],
    );

    if(strlen($arResult["VARIABLES"]["SECTION_CODE"])>0) {
        $arFilter["=CODE"] = $arResult["VARIABLES"]["SECTION_CODE"];
    }
    elseif($arResult["VARIABLES"]["SECTION_ID"]>0) {
        $arFilter["ID"] = $arResult["VARIABLES"]["SECTION_ID"];
    }

    $obCache = new CPHPCache;
    if($obCache->InitCache(36000, serialize($arFilter), "/iblock/catalog")) {
        $arCurSection = $obCache->GetVars();
    } else {
        $arCurSection = array();
        $dbRes = CIBlockSection::GetList(array(), $arFilter, false, array("ID"));
        $dbRes = new CIBlockResult($dbRes);

        if(defined("BX_COMP_MANAGED_CACHE")) {
            global $CACHE_MANAGER;
            $CACHE_MANAGER->StartTagCache("/iblock/catalog");

            if ($arCurSection = $dbRes->GetNext())
            {
                $CACHE_MANAGER->RegisterTag("iblock_id_".$arParams["IBLOCK_ID"]);
            }
            $CACHE_MANAGER->EndTagCache();
        } elseif(!$arCurSection = $dbRes->GetNext()) {
            $arCurSection = array();
        }

        $obCache->EndDataCache($arCurSection);
    }

    if(!$arResult["VARIABLES"]) {
        $arCurSection['ID'] = null;
    }
}?>

<?
$APPLICATION->IncludeComponent(
    "bitrix:catalog.smart.filter",
    "",
    array(
        "IBLOCK_TYPE" => $arParams["IBLOCK_TYPE"],
        "IBLOCK_ID" => $arParams["IBLOCK_ID"],
        "SECTION_ID" => $arCurSection['ID'],
        "FILTER_NAME" => $arParams["FILTER_NAME"],
        "PRICE_CODE" => $arParams["PRICE_CODE"],
        "CACHE_TYPE" => $arParams["CACHE_TYPE"],
        "CACHE_TIME" => $arParams["CACHE_TIME"],
        "CACHE_GROUPS" => $arParams["CACHE_GROUPS"],
        "SAVE_IN_SESSION" => "N",
        "FILTER_VIEW_MODE" => $arParams["FILTER_VIEW_MODE"],
        "XML_EXPORT" => "Y",
        "SECTION_TITLE" => "NAME",
        "SECTION_DESCRIPTION" => "DESCRIPTION",
        'HIDE_NOT_AVAILABLE' => $arParams["HIDE_NOT_AVAILABLE"],
        "TEMPLATE_THEME" => $arParams["TEMPLATE_THEME"],
        'CONVERT_CURRENCY' => $arParams['CONVERT_CURRENCY'],
        'CURRENCY_ID' => $arParams['CURRENCY_ID'],
        "SEF_MODE" => $arParams["SEF_MODE"],
        "SEF_RULE" => $arResult["FOLDER"].$arResult["URL_TEMPLATES"]["smart_filter"],
        "SMART_FILTER_PATH" => $arResult["VARIABLES"]["SMART_FILTER_PATH"],
        "PAGER_PARAMS_NAME" => $arParams["PAGER_PARAMS_NAME"],
        "INSTANT_RELOAD" => $arParams["INSTANT_RELOAD"],
    ),
    $component,
    array('HIDE_ICONS' => 'Y')
);
?>
```
