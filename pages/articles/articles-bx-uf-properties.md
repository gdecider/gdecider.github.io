---
title: Работа с пользовательскими свойствами в Битрикс
keywords: пользовательские свойства битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-uf-properties.html
summary: Примеры работы с пользовательскими свойствами в CMS 1С Битрикс
toc: false
---

## Получение значения свойства

```php
<?php
/**
 * Получаем пользовательское свойство
 * Пример GetUserField ("BLOG_COMMENT", $CommentID, "UF_RATING");
 * @param $entity_id
 * @param $value_id
 * @param $property_id
 * @return mixed
 */
public static function getUserField($entity_id, $value_id, $property_id)
{
    $arUF = $GLOBALS["USER_FIELD_MANAGER"]->GetUserFields($entity_id, $value_id);
    return $arUF[$property_id]["VALUE"];
}
```
