---
title: Back-end Шпаргалка Битрикс
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_back-shpora.html
summary: false
toc: true
---

## Back-end Шпаргалка Битрикс

### Подключение скриптов и стилей вне шаблона

```php
$assets = \Bitrix\Main\Page\Asset::getInstance();

// CSS
$assets->addCss(SITE_TEMPLATE_PATH . '/css/vendor.min.css');

// JS
$assets->addJs(SITE_TEMPLATE_PATH . '/js/vendor.min.js');

// STRING
$assets->addString("<link href='http://fonts.googleapis.com/css?family=PT+Sans:400&subset=cyrillic' rel='stylesheet' type='text/css'>"); 
```

### Подключение скриптов и стилей в шаблоне

```php
// CSS
$this->addExternalCss("/local/styles.css"); 

// JS
$this->addExternalJS("/local/libs.js"); 
```

### Подключение файлов

```php
// С возможностью правки из пользовательской части
<?$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . '/inc/popupForms.php');?>

// Без возможности правки из пользовательской части
<?$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . '/inc/popupForms.php', [], ['SHOW_BORDER' => false]);?>

// С указанием типа правки php, html, text
<?$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . '/inc/popupForms.php', [], ["MODE" => "php"]); ?>

// Через включаемую область
<?$APPLICATION->IncludeComponent(
    "bitrix:main.include",
    "",
    Array(
        "AREA_FILE_SHOW" => "file",
        "AREA_FILE_SUFFIX" => "",
        "EDIT_TEMPLATE" => "",
        "PATH" => SITE_TEMPLATE_PATH."/inc/partName.php"
    )
);?>
```

### Стандартная проверка в подключаемых файлах

```php
<?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
```

### Данные текущей страницы

```php
<?php
$curPage = $APPLICATION->GetCurPage(true);
```

### Примеры применения констант

```php
<html lang="<?=LANGUAGE_ID?>">

<link rel="shortcut icon" href="<?=SITE_TEMPLATE_PATH?>/favicon/favicon.png">
```

### Вывод заголовка окна браузера

```php
<title><?$APPLICATION->ShowTitle()?></title>
```

### Вывод подключенных скриптов и стилей в head

```php
<?php
/**
* BITRIX ->ShowHead()
*/
$APPLICATION->ShowMeta("robots", false);
$APPLICATION->ShowMeta("keywords", false);
$APPLICATION->ShowMeta("description", false);
$APPLICATION->ShowLink("canonical", null);
$APPLICATION->ShowCSS(true);
$APPLICATION->ShowHeadStrings();
$APPLICATION->ShowHeadScripts();
```

### Вывод панели администрирования в пользовательской части

```php
<div id="panel"><?$APPLICATION->ShowPanel();?></div>
```

### Определение принадлежности к странице или разделу

```php
<?php
if (!CSite::InDir('/index.php')) :
    // все страницы кроме главной
endif;

if (CSite::InDir('/catalog/')) :
    // все страницы и подразделы раздела каталог
endif;

if (CSite::InDir('/catalog/index.php')) :
    // только основная страница раздела каталог
endif;
```

### Получение объектов приложения, контекста и запроса

```php
<?php
// полный вариант
$application = \Bitrix\Main\Application::getInstance();
$context = $application->getContext();
$request = $context->getRequest();

// короткий вариант получения данных запроса
$request = \Bitrix\Main\Context::getCurrent()->getRequest();

// истпользование объекта запроса

// получение GET параметра
$varName = $request['varName'];

// или тоже самое
$varName = $request->getQuery('varName');

// получение POST параметра
$varName = $request->getPost('varName');

// проверка типа запроса
if ($request->isPost()) {
	echo 'is post request';
}
```

### Подключение модулей

```php
<?php
// Статический метод подключает модуль по его имени.
\Bitrix\Main\Loader::includeModule($moduleName);

// Статический метод подключает партнёрский модуль по его имени.
\Bitrix\Main\Loader::includeSharewareModule($partnerModuleName);
```

### Работа с фреймом

```php
<?
$frame = new \Bitrix\Main\Page\FrameBuffered("city_dynamic");
$frame->begin();

    if(isset($_GET['city'])) {
        $_SESSION['USER_CITY'] = $_GET['city'];
    }
    elseif(!isset($_SESSION['USER_CITY']) || !trim($_SESSION['USER_CITY'])) {
        $_SESSION['USER_CITY'] = CCommon::getCityByIP();
    }?>
    
    <?=$_SESSION['USER_CITY']?>

<?$frame->beginStub();?>
    Определяется....
<?$frame->end();?>
```

### Формирование ссылки для логина 

```php
// пример формирование ссылок "Logout" и "Регистрация"

<?if ($USER->IsAuthorized()):?>

 <a href="<?echo $APPLICATION->GetCurPageParam("logout=yes", array(
     "login",
     "logout",
     "register",
     "forgot_password",
     "change_password"));?>">Закончить сеанс (logout)</a>

<?else:?>
 
 <a href="<?echo $APPLICATION->GetCurPageParam("register=yes", array(
     "login",
     "logout",
     "forgot_password",
     "change_password"));?>">Регистрация</a>

<?endif;?>
```

```php
<?
// Подключение модуля 
use Bitrix\Main\Loader;

Loader::includeModule("sale");

// или
\Bitrix\Main\Loader::includeModule('sale');
?>
```

### Редирект

```php
<?php
/* LocalRedirect(
 string url,
 bool skip_security_check=false,
 string status="302 Found"
); */

// Простой 302 редирект
LocalRedirect('/some_page/');

// 301 редирект с проверкой модуля безопасности на фишинг
LocalRedirect('/some_page/', false, "301 Moved permanently");

// 301 редирект БЕЗ проверкой модуля безопасности на фишинг
LocalRedirect('/some_page/', true, "301 Moved permanently");
```

### Проверка наличия группы у пользователя

```php
<?php

// Проверка идет на соответствие группам с оператором ИЛИ, т.е. согласно приведенному примеру, под выборку попадут пользователи с группой "4" или "5", в том числе пользователи, состоящие в обеих группах.

if ( CSite::InGroup( array(4,5) ) ) {
    // если указанные группы есть, то выполняем код
}
```

### AJAX в отдельном файле

```php
<?require_once($_SERVER['DOCUMENT_ROOT']. "/bitrix/modules/main/include/prolog_before.php");?>

// ... some code ...
// с этой ф-ей были проблемы
//\CMain::finalActions();
die();
```

### AJAX в странице или компоненте

```php
// Для аякса нужно получать данные без лишнего, 
// это можно сделать, если сбросить буфер в нужном месте 
// и сделать finalActions() в конце данных
$isAjax = ($_GET["ajax_list"]=="Y");
if($isAjax) {
    $APPLICATION->restartBuffer();
    $tm=".default_ajax_new"; // можем подменить шаблон именно для аякс
    $arParams["CACHE_TYPE"]="N";
}?>

... подключаем компонент получаем данные и делаем все что нужно ...

<? if($isAjax) {
    // с этой ф-ей были проблемы
    // $APPLICATION->FinalActions();
    die();
}?>
```

### Генерация пароля

```php
<?
$new_password = randString(7);
?>
```

### Создание своей отложенной функции

[Документация](https://dev.1c-bitrix.ru/api_help/main/reference/cmain/addbuffercontent.php)

```php
<? // выводим разметку в зависимости от параметра страницы "show_side_menu"
$APPLICATION->AddBufferContent(array('MyClass', 'showCondSide'));?>
```
```php
<?
class MyClass {
    /**
     * выводим разметку в зависимости от параметра страницы "show_side_menu"
     * @return string
     * */
    public function showCondSide() {
        global $APPLICATION;
        $bShowSide = $APPLICATION->GetPageProperty("show_side_menu");

        $props = $APPLICATION->GetPagePropertyList();

        ob_start();
	
            if($bShowSide):?>
                <div class="col-md-3">

                    <?$APPLICATION->IncludeComponent(
                        "bitrix:menu",
                        "sub_menu",
                        Array(
                            "ALLOW_MULTI_SELECT" => "N",
                            "CHILD_MENU_TYPE" => "left",
                            "DELAY" => "N",
                            "MAX_LEVEL" => "1",
                            "MENU_CACHE_GET_VARS" => array(0=>"",),
                            "MENU_CACHE_TIME" => "3600",
                            "MENU_CACHE_TYPE" => "N",
                            "MENU_CACHE_USE_GROUPS" => "Y",
                            "ROOT_MENU_TYPE" => "main_popup_sub",
                            "USE_EXT" => "N"
                        )
                    );?>

                </div>

                <div class="col-md-9 page-content">                        

            <?else : ?>
            
                <div class="col-md-12 page-content">
            
            <?endif;?>
            <?
            $buferResult = ob_get_contents();
        ob_end_clean();

        return $buferResult;
    }
}
```

### Буферизация и вывод разметки в нужном месте

[Документация](https://dev.1c-bitrix.ru/api_help/main/reference/cmain/showviewcontent.php)

Добавляем ссылку в h1 в шаблоне компонента header.php:

```php
<h1><?=$APPLICATION->ShowTitle();?><?$APPLICATION->ShowViewContent('news_detail');?></h1>
```

Добавляем в шаблон компонента:

```php
<?$this->SetViewTarget('news_detail');?>
   <noindex><a rel="nofollow" class="h1-head fancy" href="/develop/change_cover_type.php"><?=$arDataFilter["NAME"]?></a></noindex>
<?$this->EndViewTarget();?> 
```

### Передача данных формы

Стандартная проверка соответствия сессии

В разметке формы

```php
<form action="" name="form-order" method="post">
	<?=bitrix_sessid_post();?>
	<!-- ... разметка ... -->
</form>
```

В коде валидации формы проверяем

```php
<?php
if(!check_bitrix_sessid()) {
    $valid = false;
}
```

### Добавление данных в кэш при кастомизации стандартных компонентов

В файлах result_modifier.php можно получить объект компонента и добавить нужные данные в кэш для последующего использования. 
[подробнее тут](/articles_bx-component-epilog-example.html)

```php
// $this->__component
// или
// $this->getComponent()

$arItemsIds = ['some', 'data', 'for', 'cache'];

$keyName = 'ITEMS_IDS';
if (is_object($this->__component)) {
    $cp = $this->__component;
    $cp->arResult[$keyName] = $arItemsIds;
    $cp->SetResultCacheKeys(array($keyName));

    if (!isset($arResult[$keyName])) {
        $arResult[$keyName] = $cp->arResult[$keyName];
    }
}
```

### Перенос скриптов в нижнюю часть страницы

В админке битрикса есть настройка, отвечающая за перенос всего JS в нижнюю часть страницы, активируется она по пути:

```Настройки -> Настройки продукта -> Настройки модулей -> Главный модуль -> Переместить весь Javascript в конец страницы```

Если активировать эту настройку, то Битрикс сформирует страницу, вырежет **ВСЕ** вхождения тега script и вставит их в нижнюю часть страницы. Такой подход устраивает не всегда, яркий пример - подключение google tag manager и прочих скриптов, которые должны быть в верхней части страницы.

Что бы настройка битрикса не меняла положение скрипта в коде нужно добавить атрибут **data-skip-moving** :
```
<script data-skip-moving="true">
```
