---
title: Статьи 1С Битрикс | Проблемы и решение
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_bx_problems.html
summary: false
toc: false
---

[https://dev.1c-bitrix.ru/support/faq/8370/](https://dev.1c-bitrix.ru/support/faq/8370/)

## Ошибка "Ошибка изменения свойства [код св-ва] Название св-ва"

Может появляться при попытке изменения инфоблока, так же вместо этой ошибки при изменении инфоблока может перекидывать на форму создания вместо сохранения результатов.

#### Решение

Проверить корректность настройки окружения, а именно настройку директивы max_input_vars >= 10000.

## Поиск битрикса не ищет по части слова

По части слова стандартный поиск битрикса находит не все товары.

#### Решение

* Изменить настройки
  ```
  Настройки -> 
  Настройки продукта -> 
  Настройки модулей -> 
  Поиск -> 
  Морфология ->
  Символы, по которым не производится разделение документа на слова (при морфологическом анализе)
  ```
  **Убрать дефис**
  
* Выполнить переиндексацию ```Настройки -> Поиск -> Переиндексация```
* Перед выводом компонента ```search.page``` добавить код

  ```php
  <?php
  \Bitrix\Main\Loader::includeModule('search');
  $q = $_REQUEST['q'];
  $obSearch = new CSearch();
  $obSearch->Search([
     "QUERY" => $q,
     "SITE_ID" => LANG,
  ]);
  if ( $obSearch->errorno == 0 
    && !($arResult = $obSearch->GetNext())
    && empty($arResult)
  ) {
       $_REQUEST['q'] = '"' . $_GET['q'] . '"';
  }?>
  ```
  
## Ошибка "Class 'Bitrix\Lists\Update\EcrmPropertyUpdate' not found (0)"
  
Может возникать после обновления системы. Связана с работой агента.
  
#### Решение

В файле ```/bitrix/php_interface/dbconn.php``` добавить константу для остановки работы агентов:

```php
<?
define('NO_AGENT_CHECK', true);
?>
```

После этого можно войти в админку и деактивировать агент:

**Модуль агента "lists", функция агента "Bitrix\Lists\Update\EcrmPropertyUpdate::execAgent();"**

Или удалить агент кодом:

```php
<?
\CAgent::removeAgent('Bitrix\Lists\Update\EcrmPropertyUpdate::execAgent();', 'lists');
?>
```

После удаления агента возвращаем работоспособность агентам...

```php
<?
define('NO_AGENT_CHECK', false);
?>
```
