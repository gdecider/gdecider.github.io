---
title: Статьи 1С Битрикс Настройка ЛК
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx_lk.html
summary: false
toc: true
---

## Настройка регистрации, авторизации и восстановления пароля

### Настройка разделов

Список путей:

  * Основная страница ЛК ```/client/```

  * Форма регистрации ```/client/registration/```

  * Форма авторизации ```/client/auth/```

  * Форма восстановления пароля ```/client/forgotpasswd/```
  
  * Профиль пользователя ```/client/profile/```
  
  * Корзина ```/client/cart/```
  
  * Оформление заказа ```/client/order/```
  
  * Сравнение товаров ```/catalog/compare/```

### Стартовая страница ЛК

Проверим факт авторизации пользователя и, если авторизован, то покажем меню, иначе перекинем на форму авторизации

```php
<?
require($_SERVER["DOCUMENT_ROOT"]."/bitrix/header.php");
$APPLICATION->SetTitle("Личный кабинет");
?>
    <?if (!$USER->IsAuthorized()) {
        LocalRedirect('/client/auth/');
    }?>

    <ul>
        <li><a href="/client/profile/">Мой профиль</a></li>
        <? // часть меню для пользователей из групп 4 или 5
        if ( CSite::InGroup( array(4,5) ) ) :?>
            <li><a href="/client/profile/">Меню не для всех</a></li>
        <?endif;?>
    </ul>

<?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/footer.php");?>
```

### Страница авторизации

**Наличие кнопок авторизации через соц. сети настраивается в админке модуль "Социальные сервисы"**

```php
<?
require($_SERVER["DOCUMENT_ROOT"]."/bitrix/header.php");
$APPLICATION->SetTitle("Авторизация");
?>

<?
if ($USER->IsAuthorized()) {

    $page = '/';

    if (isset($_REQUEST['backurl']) && $_REQUEST['backurl']) {
        $page = $_REQUEST['backurl'];
        $arCurrent = parse_url($_SERVER['REQUEST_URI']);

        if(isset($arCurrent['path']) && trim($arCurrent['path'], '/') == trim($page, '/')) {
            $page = '/';
        }
    }

    // Админов не перенаправляем
    if (!$USER->IsAdmin()) {
        LocalRedirect($page);
    }
}
?>

<?$APPLICATION->IncludeComponent(
	"bitrix:system.auth.form",
	"auth",
	Array(
		"FORGOT_PASSWORD_URL" => "/client/forgotpasswd/",
		"PROFILE_URL" => "/client/profile/",
		"REGISTER_URL" => "/client/registration/",
		"SHOW_ERRORS" => "Y"
	)
);?><?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/footer.php");?>
```

### Страница регистрации

Если нужно добавить в форму нестандартные поля, то эти поля сперва нужно создать в админке.

Для создания доп.поля пользователя идем 
```
Настройки 
- Пользователи 
- список пользователей, открываем на редактирование любого пользователя 
- Доп. поля 
- Добавить пользовательское поле 
- добавляем то что нужно
```

```php
<?
require($_SERVER["DOCUMENT_ROOT"]."/bitrix/header.php");
$APPLICATION->SetTitle("Регистрация");
?>
<div class="container">
	<div class="row">
        <?$APPLICATION->IncludeComponent(
            "bitrix:main.register",
            "registration",
            array(
                "AUTH" => "Y",
                "REQUIRED_FIELDS" => array(
                    0 => "EMAIL",
                ),
                "SET_TITLE" => "Y",
                "SHOW_FIELDS" => array(
                    0 => "EMAIL",
                    1 => "NAME",
                    2 => "LAST_NAME",
                ),
                "SUCCESS_PAGE" => "/profile/",
                "USER_PROPERTY" => array(
                    0 => "UF_CARD_BRIGADA",
                ),
                "USER_PROPERTY_NAME" => "",
                "USE_BACKURL" => "Y",
                "COMPONENT_TEMPLATE" => "registration"
            ),
            false
        );?>
	</div>
</div>
<?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/footer.php");?>
```

### Ссылки на регистрацию и авторизацию

Удобно подключать громоздкие куски кода из включаемых файлов

```php
<?// в нужном месте страницы (обычно шапка)?>
<?$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH.'/inc/authInfo.php', [], ['SHOW_BORDER' => false]);?>
```

Содержимое файла ```SITE_TEMPLATE_PATH.'/inc/authInfo.php'```

```php
<?if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();?>

<?if($USER->IsAuthorized()):?>
    <div class="enter">
        <a href="<?echo $APPLICATION->GetCurPageParam("logout=yes", array(
            "login",
            "logout",
            "register",
            "forgot_password",
            "change_password"));?>">Выход</a></div>
    <div class="login"><a href="/client/profile/"><?=$USER->GetFullName()?></a></div>
<?else:?>
    <div class="enter"><a href="/client/">Вход</a></div>
    <div class="login"><a href="/client/registration/">Регистрация</a></div>
<?endif;?>
```
### Профиль пользователя

```php
<?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/header.php");
$APPLICATION->SetTitle("Ваш профиль");
?>

<?$APPLICATION->IncludeComponent(
	"bitrix:main.profile",
	"",
	Array(
		"CHECK_RIGHTS" => "N",
		"SEND_INFO" => "N",
		"SET_TITLE" => "Y",
		"USER_PROPERTY" => array(),
		"USER_PROPERTY_NAME" => ""
	)
);?>

<?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/footer.php");?>
```

### Страница корзины

```php
<?
require($_SERVER["DOCUMENT_ROOT"]."/bitrix/header.php");
$APPLICATION->SetTitle("Корзина");
?>
<?$APPLICATION->IncludeComponent(
	"bitrix:sale.basket.basket",
	"",
	Array(
		"ACTION_VARIABLE" => "basketAction",
		"AUTO_CALCULATION" => "Y",
		"COLUMNS_LIST" => array("NAME","DISCOUNT","WEIGHT","DELETE","DELAY","TYPE","PRICE","QUANTITY"),
		"CORRECT_RATIO" => "N",
		"GIFTS_BLOCK_TITLE" => "Выберите один из подарков",
		"GIFTS_CONVERT_CURRENCY" => "N",
		"GIFTS_HIDE_BLOCK_TITLE" => "N",
		"GIFTS_HIDE_NOT_AVAILABLE" => "N",
		"GIFTS_MESS_BTN_BUY" => "Выбрать",
		"GIFTS_MESS_BTN_DETAIL" => "Подробнее",
		"GIFTS_PAGE_ELEMENT_COUNT" => "4",
		"GIFTS_PLACE" => "BOTTOM",
		"GIFTS_PRODUCT_PROPS_VARIABLE" => "prop",
		"GIFTS_PRODUCT_QUANTITY_VARIABLE" => "quantity",
		"GIFTS_SHOW_DISCOUNT_PERCENT" => "Y",
		"GIFTS_SHOW_IMAGE" => "Y",
		"GIFTS_SHOW_NAME" => "Y",
		"GIFTS_SHOW_OLD_PRICE" => "N",
		"GIFTS_TEXT_LABEL_GIFT" => "Подарок",
		"HIDE_COUPON" => "N",
		"PATH_TO_ORDER" => "/client/order/",
		"PRICE_VAT_SHOW_VALUE" => "N",
		"QUANTITY_FLOAT" => "N",
		"SET_TITLE" => "Y",
		"TEMPLATE_THEME" => "blue",
		"USE_ENHANCED_ECOMMERCE" => "N",
		"USE_GIFTS" => "N",
		"USE_PREPAYMENT" => "N"
	)
);?>
<?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/footer.php");?>
```

### Малая корзина

```php
<?// обычно верхний правый угол экрана ?>
<?$APPLICATION->IncludeComponent("bitrix:sale.basket.basket.line", "cartLink", Array(
	"HIDE_ON_BASKET_PAGES" => "Y",	// Не показывать на страницах корзины и оформления заказа
		"PATH_TO_AUTHORIZE" => "/client/auth/",	// Страница авторизации
		"PATH_TO_BASKET" => "/client/cart/",	// Страница корзины
		"PATH_TO_ORDER" => "/client/order/",	// Страница оформления заказа
		"PATH_TO_PERSONAL" => "/client/",	// Страница персонального раздела
		"PATH_TO_PROFILE" => "/client/profile/",	// Страница профиля
		"PATH_TO_REGISTER" => "/client/registration/",	// Страница регистрации
		"POSITION_FIXED" => "N",	// Отображать корзину поверх шаблона
		"SHOW_AUTHOR" => "N",	// Добавить возможность авторизации
		"SHOW_DELAY" => "Y",
		"SHOW_EMPTY_VALUES" => "Y",	// Выводить нулевые значения в пустой корзине
		"SHOW_IMAGE" => "Y",
		"SHOW_NOTAVAIL" => "Y",
		"SHOW_NUM_PRODUCTS" => "Y",	// Показывать количество товаров
		"SHOW_PERSONAL_LINK" => "N",	// Отображать персональный раздел
		"SHOW_PRICE" => "Y",
		"SHOW_PRODUCTS" => "N",	// Показывать список товаров
		"SHOW_SUBSCRIBE" => "Y",
		"SHOW_SUMMARY" => "Y",
		"SHOW_TOTAL_PRICE" => "Y",	// Показывать общую сумму по товарам
	),
	false
);?>
```

### Настройка отображения факта наличия товара в корзине в шаблоне каталога

[Пример работы с component_epilog.php](/articles_bx-component-epilog-example.html)

### Информация о товарах в сравнении

Сравниваемые товары просто добавляются битриксом в сессионную переменную и хранятся в виде массива. Получить количество товаров в списке сравнения можно так:

```php
<?
$cntCompare = 0;
if(isset($_SESSION["CATALOG_COMPARE_LIST"][IBID_CATALOG]["ITEMS"])) {
    $cntCompare = count($_SESSION["CATALOG_COMPARE_LIST"][IBID_CATALOG]["ITEMS"]);
}
?>
```

Так же в Битриксе есть компонент для этих целей.
Пример его подключения на странице:

```php
<?$APPLICATION->IncludeComponent(
    "bitrix:catalog.compare.list",
    "",
    Array(
	"ACTION_VARIABLE" => "action",
	"AJAX_MODE" => "N",
	"AJAX_OPTION_ADDITIONAL" => "",
	"AJAX_OPTION_HISTORY" => "N",
	"AJAX_OPTION_JUMP" => "N",
	"AJAX_OPTION_STYLE" => "Y",
	"COMPARE_URL" => "/catalog/compare/",
	"DETAIL_URL" => "",
	"IBLOCK_ID" => IBID_CATALOG,
	"IBLOCK_TYPE" => "catalogs",
	"NAME" => "CATALOG_COMPARE_LIST",
	"POSITION" => "top left",
	"POSITION_FIXED" => "Y",
	"PRODUCT_ID_VARIABLE" => "id"
    )
);?>
```

### Страница результатов сравнения

```php
<?$APPLICATION->IncludeComponent(
    "bitrix:catalog.compare.result",
    "",
    Array(
	"ACTION_VARIABLE" => "action",
	"AJAX_MODE" => "N",
	"AJAX_OPTION_ADDITIONAL" => "",
	"AJAX_OPTION_HISTORY" => "N",
	"AJAX_OPTION_JUMP" => "N",
	"AJAX_OPTION_STYLE" => "Y",
	"BASKET_URL" => "/client/cart/",
	"CONVERT_CURRENCY" => "N",
	"DETAIL_URL" => "",
	"DISPLAY_ELEMENT_SELECT_BOX" => "N",
	"ELEMENT_SORT_FIELD" => "sort",
	"ELEMENT_SORT_FIELD_BOX" => "name",
	"ELEMENT_SORT_FIELD_BOX2" => "id",
	"ELEMENT_SORT_ORDER" => "asc",
	"ELEMENT_SORT_ORDER_BOX" => "asc",
	"ELEMENT_SORT_ORDER_BOX2" => "desc",
	"FIELD_CODE" => array("ID","NAME","PREVIEW_TEXT","PREVIEW_PICTURE","DETAIL_TEXT","DETAIL_PICTURE","DATE_ACTIVE_FROM","DATE_ACTIVE_TO"),
	"HIDE_NOT_AVAILABLE" => "N",
	"IBLOCK_ID" => IBID_CATALOG,
	"IBLOCK_TYPE" => "catalogs",
	"NAME" => "CATALOG_COMPARE_LIST",
	"PRICE_CODE" => array("BASE"),
	"PRICE_VAT_INCLUDE" => "Y",
	"PRODUCT_ID_VARIABLE" => "id",
	"PROPERTY_CODE" => array("KOLICHESTVO","KRATKOEOPISANIE","MASSAKG","OBEML","OBEMMKUB","POLNOEOPISANIE","FOTO","CENA","ARTIKUL",""),
	"SECTION_ID_VARIABLE" => "SECTION_ID",
	"SHOW_PRICE_COUNT" => "1",
	"TEMPLATE_THEME" => "blue",
	"USE_PRICE_COUNT" => "N"
    )
);?>
```
