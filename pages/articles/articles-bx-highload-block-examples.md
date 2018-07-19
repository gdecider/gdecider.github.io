---
title: Работа с highload блоками Битрикс
keywords: highload битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-highload-block-examples.html
summary: Пример работа с highload блоками в CMS 1С Битрикс
toc: false
---

## Общие статьи

```php
<?php
$arBrands = [];
try {
	\Bitrix\Main\Loader::includeModule("highloadblock");

	$hlblock = Bitrix\Highloadblock\HighloadBlockTable::getById(HL_BRANDS)->fetch();
	$entity = Bitrix\Highloadblock\HighloadBlockTable::compileEntity($hlblock);
	$entityClass = $entity->getDataClass();

	$rsData = $entityClass::getList(
		[
			"select" => ["*"],
			"order" => ["ID" => "ASC"],
			"filter" => ["!UF_FILE" => false]
		]
	);

	while($arData = $rsData->Fetch()){
		$arBrands[] = $arData;
	}

} catch (\Exception $e) {
	echo $e->getMessage();
}
```
