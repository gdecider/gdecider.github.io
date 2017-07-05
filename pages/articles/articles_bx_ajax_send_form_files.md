---
title: Статьи 1С Битрикс | AJAX Отправка формы с файлами
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx_ajax_send_form_files.html
summary: false
toc: false
---

## AJAX Отправка формы с файлами

### Задача

1) Отправить данные формы и вложенные файлы на сайте под управлением CMS Битрикс.

2) Провести проверку файлов по заданным условиям: 

  * кол-во файлов не более 5ти; 
  * допустимые типы файлов 'gif','png','jpg','jpeg','pdf','xls','xlsx','doc','docx'; 
  * размеры файлов не более 5Mb;

3) Сохранить полученные данные и файлы в инфоблок.

### Решение

#### Битрикс

Создайте инфоблок в который будут сохраняться данные заявок, свойство для хранения файлов сделайте множественным.

Код инфоблока сохраните в константу IBID_SUPPORT.

#### Форма

На странице подключим файл с формой

```php
<?$APPLICATION->IncludeFile(SITE_TEMPLATE_PATH.'/inc/supportForm.php', [], ['SHOW_BORDER' => false]);?>
```

Содержимое подключаемого файла

```php
<?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();

$assets = \Bitrix\Main\Page\Asset::getInstance();
$assets->addJs(SITE_TEMPLATE_PATH.'/js/partSupportForm.js');
?>

<form action="<?=SITE_TEMPLATE_PATH?>/ajax/saveSupport.php" class="support-form" enctype="multipart/form-data">
    <input type=text class="sfg__input" placeholder="Имя *" name="name" required>
    <input type=text class="sfg__input" placeholder="E-mail" name="email" required>
    <input type=text class="sfg__input" placeholder="Фамилия" name="fam">
    <input type=text class="sfg__input" placeholder="Номер карты клуба Друзей" name="card">
    <input type=text class="sfg__input" placeholder="Отчество" name="otch">
    <input type=text class="sfg__input" placeholder="Номер заказа" name="znum">
    <input type=text class="sfg__input" placeholder="Контактный телефон *" name="phone" required>
    <textarea placeholder="Опишите суть обращения*" name="text" required></textarea>
    <textarea placeholder="Опишите Ваше требование" name="treb" required></textarea>

    <div class="upload-note">
        Вы можете присоединить не более 3-х документов (фотографии, копии накладных и т.д.)
    </div>

    <div class="ctrFiles"></div>

    <label for="upload-btn" class="button large-btn support-form__upload-btn">
        <input type="file" id="upload-btn" class="opacity-control"/>
        прикрепить файл
    </label>

    <button type="submit" class="jsSubmit">отправить</button>
</form>

```

#### JS формы

Файл partSupportForm.js

```js
$(function () {
    $(document).on('click', '.jsSubmit', function(e) {
        e.preventDefault();
        var $this = $(this);
        var $form = $this.closest('form');
        var formUrl = $form.attr('action');

        $.ajax({
            url:formUrl,
            method: 'post',
            type: "POST",             // Type of request to be send, called as method
            data: new FormData($form.get(0)), // Data sent to server, a set of key/value pairs (i.e. form fields and values)
            contentType: false,       // The content type used when sending data to the server.
            cache: false,             // To unable request pages to be cached
            processData:false,        // To send DOMDocument or non processed data file it is set to false

            dataType:'json',
            success: function (res) {
                console.log('ok');
                console.log(res);
                if(res.msg) {
                    alert(res.msg);
                }

                if(!res.hasError) {
                    $form.find('input, textarea').val('');
                    $('.ctrFiles').html('');
                }
            },
            error: function (p1, p2, p3) {
                console.log(p1);
                console.log(p2);
                console.log(p3);
            }
        });

        return false;
    });

    var arFiles = [];
    $(document).on('change', '#upload-btn', function(e) {
        var $this = $(this);
        var $ctrFiles = $('.ctrFiles');
        var value = $this.val();
        var fileCnt = $ctrFiles.find('.item').length;

        if(fileCnt >=3) {
            alert('Вы можете добавить не более 3 файлов');
            return false;
        }

        if (value && !arFiles.includes(value)) {
            arFiles.push(value);
            $ctrFiles.append($('<div class="item"><div class="cls"></div>'+value+'</div>').append($this.clone().attr('id', 'att_'+Date.now()).attr('name', 'attach[]')));

        }

    });

    $(document).on('click', '.ctrFiles .item .cls', function(e) {
        $(this).closest('.item').remove();
    });

});
```

#### Обработка на сервере

Файл supportForm.php

```php
<?require($_SERVER["DOCUMENT_ROOT"]."/bitrix/modules/main/include/prolog_before.php");?>
<?php
use Bitrix\Main\Loader;
global $APPLICATION;

function checkFiles($files) {

    $arResult = array(
        'hasError' => true,
        'msg' => "Ошибка отправки сообщения",
    );

    // проверим размеры, количество и типы файлов
    if (count($files['size']) > 5) {
        $arResult['msg'] = "Превышен лимит количества файлов. К сообщению можно прикрепить не более 5 файлов.";
        return $arResult;
    }

    $fileSizeLimit = 1024 * 1024 * 5;
    $fileExts =  array('gif','png','jpg','jpeg','pdf','xls','xlsx','doc','docx');

    foreach ($files['size'] as $key => $size) {

        if ($size > $fileSizeLimit) {
            $arResult['msg'] = "Превышен допустимый размер файла. Разрешены файлы не более 5Mb.";
            return $arResult;
        }

        $ext = pathinfo($files['name'][$key], PATHINFO_EXTENSION);

        if(!in_array($ext, $fileExts) ) {
            $arResult['msg'] = "Прикрепить можно только файлы изображений (jpg, jpeg, png, gif) и документов (pdf, xls, xlsx, doc, docx)";
            return $arResult;
        }
    }

    $arResult = array(
        'hasError' => false,
        'msg' => "Файлы корректны",
    );

    return $arResult;
}

$request = \Bitrix\Main\Context::getCurrent()->getRequest();
$server = \Bitrix\Main\Context::getCurrent()->getServer();

$iblockId = IBID_SUPPORT;

$name = htmlspecialcharsbx($request->getPost('name'));
$fam = htmlspecialcharsbx($request->getPost('fam'));
$otch = htmlspecialcharsbx($request->getPost('otch'));
$phone = htmlspecialcharsbx($request->getPost('phone'));
$email = htmlspecialcharsbx($request->getPost('email'));
$card = htmlspecialcharsbx($request->getPost('card'));
$text = htmlspecialcharsbx($request->getPost('text'));
$treb = htmlspecialcharsbx($request->getPost('treb'));
$znum = htmlspecialcharsbx($request->getPost('znum'));
$files = $request->getFile('attach');

$arResult = array(
    'hasError' => true,
    'msg' => "Ошибка отправки сообщения",
);

if (  !$request->isPost()
   || !($name && $phone && $text)
   || !Loader::includeModule('iblock')
) {

    if(!($name && $phone && $text)) {
        $arResult['msg'] = "ОШИБКА: Заполните все обязательные поля.";
    }

    echo json_encode($arResult);
    die();
}

$arFileCheckRes = checkFiles($files);

if($arFileCheckRes['hasError']){
    echo json_encode($arFileCheckRes);
    die();
}

$arPROPS = array(
    'PHONE' => $phone,
    'EMAIL' => $email,
    'FAM' => $fam,
    'OTCH' => $otch,
    'CARD' => $card,
    'ZNUM' => $znum,
    //'FILE' => $email,
);

$arFields = array(
    "IBLOCK_SECTION_ID" => false, // элемент лежит в корне раздела
    "IBLOCK_ID" => $iblockId,
    'ACTIVE' => 'Y',
    'NAME' => $name,
    'PREVIEW_TEXT' => $text,
    'DETAIL_TEXT' => $treb,
    'PROPERTY_VALUES' => $arPROPS,
);


$el = new \CIBlockElement();
$itemId = $el->Add($arFields);

if (!$itemId) {
    echo json_encode($arResult);
    die();
}

// сохраняем файлы в Битрикс
$fileUploadDir = $server->getDocumentRoot().'/upload/';
$arFiles = array();
foreach ($files['size'] as $key => $size) {
    $arFiles[] = array(
        'name' => $files['name'][$key],
        'type' => $files['type'][$key],
        'tmp_name' => $files['tmp_name'][$key],
        'error' => $files['error'][$key],
        'size' => $files['size'][$key],
    );
}
$arFileBx = array();
$cnt = count($arFiles);
for ($i = 0; $i < $cnt; $i++) {

    $arFiles[$i]['save_to'] = $fileUploadDir.$itemId.'_'.date('Ymd_His_').$arFiles[$i]['name'];

    if(!move_uploaded_file($arFiles[$i]['tmp_name'], $arFiles[$i]['save_to'])){
        $arResult['msg'] .= "Ошибка загрузки файла ".$arFiles[$i]['name']."\n";
    }

    $arFileBx[] = array("VALUE" => CFile::MakeFileArray($arFiles[$i]['save_to']),"DESCRIPTION"=>"");

}

if(count($arFileBx) > 0) {
    CIBlockElement::SetPropertyValuesEx($itemId, $iblockId, array('FILE' => $arFileBx));
    foreach ( $arFiles as $file ) {
        unlink($file['save_to']);
    }
}

$arResult = array(
    'hasError' => false,
    'msg' => "Спасибо! Ваше обращение отправлено.",
);

echo json_encode($arResult);
die();
?>

```