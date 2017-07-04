---
title: Статьи 1С | Примеры фильтра для GetList и компонентов
keywords: sample homepage
sidebar: articles_sidebar
permalink: articles_bx-filter-getlist-examples.html
summary: false
toc: false
---

## Примеры фильтра для GetList и компонентов

Приведены примеры часто используемых настроек при фильтрации элементов

### Убрать из выдачи товары БЕЗ изображений

```php
$arrFilterSearch = [
	'!PREVIEW_PICTURE' => false,
];
```

### Убрать из выдачи товары БЕЗ цены (по типу цен)

```php
// не забываем выносить все значимые данные 
// в файл констант /local/php_interface/include/constants.php

define("PRICE_ROZNICA_ID", 47);

$arrFilterSearch = [
	'>CATALOG_PRICE_'.PRICE_ROZNICA_ID => '0',
];
```

### Фильтруем данные по условию "ИЛИ"

```php

// Получим товары у которых свойство TSENA_DO_AKTSII пусто или меньше 0
$arrFilterSearch = [
	[
		"LOGIC" => "OR",
		['PROPERTY_TSENA_DO_AKTSII' => false],
		['<PROPERTY_TSENA_DO_AKTSII_VALUE' => 0],
	]
];
```