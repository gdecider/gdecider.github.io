---
title: Back-end Шпаргалка Битрикс
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_back-shpora.html
summary: false
toc: true
---
## Шпаргалка PHP

### Валидация email стандартными средствами PHP

```php
<?
$isValidEmail = filter_var($emailForCheck, FILTER_VALIDATE_EMAIL);
```

### Форматирование цены

```php
<?
function setFormat($fSum) {
    return number_format ( $fSum, 0, '.', ' ' );
}
```

### Вызов статичиского метода класса с получением имени метода из переменной

```php
<?php
class SomeClass 
{
    static private function validateEmail($value) 
    {
        return filter_var($value, FILTER_VALIDATE_EMAIL);
    }
    
    public function someMethod() {
	
        $field = [
	    "value" => "q@q.com",
	    "valid" => true,
	    "func"  => "validateEmail",
        ];
	
        $func = __CLASS__ . '::' . $field['func'];
        $field['valid'] = $func($field['value']);
	
	return $field;
    }
}
```

### Экономим место при хранении массивов для чтения

Часто нужно сохранить одни и те же данные в массив, но иметь разный индекс у элементов, для экономии места и ресурсов можно сохранять данные по ссылке, **нужно помнить что так лучше делать когда данные нужны только для чтения в коде, т.к. при модификации таких массивов появятся трудно уловимые зависомости**

```php
<?php
$productsById = [];
$productsByCode = [];

while ($itemsFromDb as $item) {
    $productsById[$item['ID']] = $item;
    $productsByCode[$item['CODE']] = &$productsById[$item['ID']];
}
```

## Back-end Шпаргалка Битрикс

### Подключение скриптов и стилей вне шаблона

```php
<?php
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
<?php
// CSS
$this->addExternalCss("/local/styles.css"); 

// JS
$this->addExternalJS("/local/libs.js"); 
```

### Подключение файлов

* Метод [IncludeFile](https://dev.1c-bitrix.ru/api_help/main/reference/cmain/includefile.php)

  ```php
  <?
  // С возможностью правки из пользовательской части
  $APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . '/include/popupForms.php');

  // Без возможности правки из пользовательской части
  $APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . '/include/popupForms.php', false, ['SHOW_BORDER' => false]);

  // С указанием типа правки php, html, text
  $APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . '/include/popupForms.php', false, ["MODE" => "php"]);

  // С указанием передачей переменных в файл (в файле будет доступна переменная $name)
  $APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . '/include/popupForms.php', ['name' => $arResult['NAME']], ['SHOW_BORDER' => false]); 
  ```
  
* Компонент "включаемая область"

  ```php
  <?
  $APPLICATION->IncludeComponent(
      "bitrix:main.include",
      "",
      Array(
          "AREA_FILE_SHOW" => "file",
          "AREA_FILE_SUFFIX" => "",
          "EDIT_TEMPLATE" => "",
          "PATH" => SITE_TEMPLATE_PATH."/include/partName.php"
      )
  );
  
  // Скрываем служебные кнопки в режиме редактирования
  $APPLICATION->IncludeComponent(
      "bitrix:main.include",
      "",
      Array(
          "AREA_FILE_SHOW" => "file",
          "AREA_FILE_SUFFIX" => "",
          "EDIT_TEMPLATE" => "",
          "PATH" => SITE_TEMPLATE_PATH."/include/partName.php"
      ), false, ["HIDE_ICONS"=>true]
  );  
  ```

### Стандартная проверка в подключаемых файлах

```php
<? if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die(); ?>
```

### Переменные в шаблоне компонента (стандартное начало template.php)

```php
<? if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) die();
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
?>
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

### Изменение размеров изображения

```php
<?
if($arResult['DETAIL_PICTURE']) {
	$filters = [
		["name" => "sharpen", "precision" => 15],
	];
	$sizes = ['width' => 420, 'height' => 300];
	$arResult['DETAIL_PICTURE']['SRC'] = \CFile::ResizeImageGet(
		$arResult['DETAIL_PICTURE']['ID'],
		$sizes, BX_RESIZE_IMAGE_PROPORTIONAL, true,
		$filters, false, 85)['src'];
}
?>
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

### Композит 

[Официальный курс](https://dev.1c-bitrix.ru/learning/course/?COURSE_ID=39&INDEX=Y)

* Голосование компонента "ЗА" композит

  ```php
  <?php
  $this->setFrameMode(true);
  ```
  
* Выделение части кода для догрузки композитом
  
  **В шаблоне**
  
  ```php
  <?$frame = $this->createFrame("top-search-input-container", false)->begin();?>
    Динамическая часть, подгрузится позже
  <?$frame->beginStub();?>    
    Заглушка
  <?$frame->end();?>
  ```

  **В других частях сайта**
  
  **Способ 1 - красивый**
  
  ```php
  <?
  $frame = new \Bitrix\Main\Page\FrameBuffered("city_dynamic");
  $frame->begin();?>
    Динамическая часть, подгрузится позже
  <?$frame->beginStub();?>
    Заглушка
  <?$frame->end();?>
  ```
  
  **Способ 2 - статичный, для помещения в него компонентов и отложенных функций**
  
  ```php
  <? // открываем динамическую зону
  $dynamicArea = new \Bitrix\Main\Composite\StaticArea("my_dynamic");
  
  // опционально
  // $dynamicArea->setAnimation(true);
  // $dynamicArea->setStub("заглушка");
  // $dynamicArea->setContainerID("my-div-id");
  
  $dynamicArea->startDynamicArea();?>
  
  <? // тут динамическая зона, к примеру отложенная функция
  $APPLICATION->ShowViewContent('someMarker'); ?>
  
  <? // закрываем динамическую зону
  $dynamicArea->finishDynamicArea(); ?>
  ```
  

### Формирование ссылки для логина 

```php
<?
// пример формирование ссылок "Logout" и "Регистрация"
?>
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
<?
define("NO_KEEP_STATISTIC", true);
define("NO_AGENT_STATISTIC", true);
define("NOT_CHECK_PERMISSIONS", true);

require_once($_SERVER['DOCUMENT_ROOT']. "/bitrix/modules/main/include/prolog_before.php");

$context = \Bitrix\Main\Application::getInstance()->getContext();

$response = new \Bitrix\Main\HttpResponse($context);
$response->addHeader("Content-Type", "application/json");

$request = $context->getRequest();
$request->addFilter(new Bitrix\Main\Web\PostDecodeFilter);

$arResult = [
    'status' => 'success',
    'text'   => '',
];

if (!$request->isAjaxRequest())
{
    $arResult = [
        'status' => 'error',
        'text'   => 'Request is not XHR',
    ];

    $response->flush(Bitrix\Main\Web\Json::encode($arResult));

    die();
}

if (!$request->isPost())
{
    $arResult = [
        'status' => 'error',
        'text'   => 'Request is not POST',
    ];

    $response->flush(Bitrix\Main\Web\Json::encode($arResult));

    die();
}

// выполняем вычисления...

// возвращаем результат
$response->flush(Bitrix\Main\Web\Json::encode($arResult));
```

### Очистка буфера вывода

Если уровней вложенности буферов много, то может не сработать

```php
<?php
$APPLICATION->restartBuffer();
```

Гарантированная очистка всех уровней вложенности буферов вывода

```php
<?php
while (ob_get_level()) {
    ob_end_clean();
}
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

Помещение контента в буфер, для последующего его вывода:

* вариант 1 [SetViewTarget](https://dev.1c-bitrix.ru/api_help/main/reference/cmain/showviewcontent.php)

```php
<?$this->SetViewTarget('news_detail');?>
    <a href="#">Some buffered data</a>
<?$this->EndViewTarget();?> 
```

* вариант 2 [AddViewContent](https://dev.1c-bitrix.ru/api_help/main/reference/cmain/addviewcontent.php)

```php
<? ob_start(); ?>
    <a href="#">Some buffered data</a>
<? $APPLICATION->AddViewContent('news_detail', ob_get_clean());?>

```

Вывод буферизированной области в нужном месте [ShowViewContent](https://dev.1c-bitrix.ru/api_help/main/reference/cmain/showviewcontent.php):

```php
<?$APPLICATION->ShowViewContent('news_detail');?>
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

### Работа с датами

```php
<?php
use Bitrix\Main\Type\DateTime;

$objDateTime = DateTime::createFromPhp(new \DateTime('2000-01-01'));
$objDateTime = DateTime::createFromTimestamp(1346506620);

// или
// Текущее время:
$objDateTime = new DateTime();
// Из строки в формате текущего сайта
$objDateTime = new DateTime("25.12.2012 12:30:00");
// Из строки с указанием формата:
$objDateTime = new DateTime("2007-05-14 12:10:00", "Y-m-d H:i:s");

// Из объекта можно получить представление в виде timestamp:
echo $objDateTime->getTimestamp();

// в виде строки в формате текущего сайта:
echo $objDateTime->toString();

// в произвольном формате (фактически обёртка над DateTime::format):
echo $objDateTime->format("Y-m-d H:i:s");

// Метод add реализует сложение и вычитание дат, можно указывать смещение словами years, months, days, weeks, hours, minutes, seconds и знаками +/-:

$objDateTime = new DateTime("01.01.2012 00:00:00"); // "2012-01-01 00:00:00"
$objDateTime->add("1 day"); // "2012-01-01 00:00:00" => "2012-01-02 00:00:00"
$objDateTime->add("-1 day"); // "2012-01-01 00:00:00" =>"2011-12-31 00:00:00"
$objDateTime->add("3 months - 5 days + 10 minutes"); // "2012-01-01 00:00:00" =>"2012-03-27 00:10:00"

// Метод add изменяет объект, здесь для наглядности приведены результаты вызова add с начального состояния $objDateTime.
// Также в add можно указывать смещение в формате DateInterval (но буква P в начале строки необязательна):

$objDateTime = new DateTime("01.01.2012 00:00:00"); // "2012-01-01 00:00:00"
$objDateTime->add("7M5DT2M"); // "2012-01-01 00:00:00" =>"2012-08-06 00:02:00"
$objDateTime->add("-2YT10M"); // "2012-01-01 00:00:00" =>"2009-12-31 23:50:00"

// Получение формата даты сайта
$siteDateShortFormat = \CSite::GetDateFormat("SHORT");
$siteDateFullFormat = \CSite::GetDateFormat("FULL");

// Преобразование формата сайта к формату PHP
$phpDateShortFormat = \Bitrix\Main\Type\Date::convertFormatToPhp($siteDateShortFormat);

// Пример из компонента новостей Пример формата "j F Y"
$arItem["DISPLAY_ACTIVE_FROM"] = CIBlockFormatProperties::DateFormat($arParams["ACTIVE_DATE_FORMAT"], MakeTimeStamp($arItem["ACTIVE_FROM"], CSite::GetDateFormat()));

```

### Узнать сконвертированы заказы интернет-магазина в новый формат или нет

```php
<?
$isOrderConverted = \Bitrix\Main\Config\Option::get("main", "~sale_converted_15", 'N');

if ($isOrderConverted == "Y"){
	echo "is converted";
} else {
	echo "not converted";
}
```

### CRON скрипт

```php
<?
// путь к корню сайта
$_SERVER["DOCUMENT_ROOT"] = realpath(__DIR__ . '/../../../../');

define("NO_KEEP_STATISTIC", true);
define("NOT_CHECK_PERMISSIONS",true);
define('CHK_EVENT', true);

require($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/prolog_before.php");

@set_time_limit(0);
@ignore_user_abort(true);

// тут пишем код

require($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/epilog_after.php");
```

### Вывод ошибки в административной части

```php
<?php
require_once($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/prolog_admin_before.php");

// ... some code ...

if ($hasError)
{
    require($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/prolog_admin_after.php");
    CAdminMessage::ShowMessage(array(
	"MESSAGE" => 'Some error message',
	"DETAILS" => 'Some error details',
	"HTML" => true,
	"TYPE" => "ERROR"
    ));
	
    require($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/epilog_admin.php");
    return;
}
```
