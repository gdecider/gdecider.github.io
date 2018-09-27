---
title: Обработка текста включаемой области перед выводом | Статьи CMS "1С Битрикс"
keywords: работа с включаемой областью Битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-main-include-preload.html
summary: false
toc: false
---

### Задача

Нужно вывести включаемую область на сайте, с предварительной обработкой текста, при этом оставив все стандартные возможности ее редактирования.

### Решение

Основной момент, который нам нужно помнить это то, что **включаемая область является обычным компонентом Битрикс**. Это означает что мы можем зоздавать нужные нам шаблоны вывода, в которых и нужно добавить необходимую логику обработки текста.

Для решения поставленной задачи создайте файл шаблона включаемой области в папке используемого на сайте шаблона ```<site_root>/local/templates/templateName/components/bitrix/main.include/newTemplateName/template.php```

В коде шаблона или в файлах result_modifier.php, component_epilog.php? все зависит от нужной логики преобразования, выполните то что нужно, например:

```php
<?
if (!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED !== true) {
    die();
}
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

if ($arResult["FILE"] <> '') {
    ob_start();
    include($arResult["FILE"]);
    $text = ob_get_clean();
    echo str_replace('find_text','replace_on_it', $text);
}
?>
```

В нужном месте сайта для вывода включаемой области используйне нужный шаблон:

```php
<? $APPLICATION->IncludeComponent(
    "bitrix:main.include",
    "newTemplateName",
    Array(
        "AREA_FILE_RECURSIVE" => "N",
        "AREA_FILE_SHOW" => "sect",
        "AREA_FILE_SUFFIX" => "file-name",
        "EDIT_TEMPLATE" => "Y"
    ),
    false,
    ['SHOW_BORDER' => false]
); ?>
```

Для вывода телефона или email в адресе ссылки можно поступить так:

```php
<? 
ob_start();
$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH . "/include/phone.php", [], ['SHOW_BORDER' => false] );
$phone = ob_get_clean(); 
?>
<a href="tel:<?=str_replace([' ','(',')'],'',$phone);?>">
    <? $APPLICATION->IncludeComponent(
		"bitrix:main.include",
	    "",
		Array(
			"AREA_FILE_SHOW" => "file",
		    "AREA_FILE_SUFFIX" => "",
			"EDIT_TEMPLATE" => "",
			"PATH" => SITE_TEMPLATE_PATH."/include/phone.php"
		)
    );?>
</a>
```
