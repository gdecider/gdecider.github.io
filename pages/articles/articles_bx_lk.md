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

### Настройка стартовой страницы ЛК

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

### Настройка страницы авторизации

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

### Настройка страницы регистрации

Если нужно добавить в форму нестандартные поля, то эти поля сперва нужно создать в админке.

Для создания доп.поля пользователя идем ```Настройки - Пользователи - список пользователей, открываем на редактирование любого пользователя - Доп. поля - Добавить пользовательское поле - добавляем то что нужно```

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
### Настройка корзины

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
