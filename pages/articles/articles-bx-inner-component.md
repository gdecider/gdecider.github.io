---
title: Выводим компонент в шаблоне другого компонента | Статьи CMS "1С Битрикс"
keywords: вложенный вывод компонента в Битрикс, выводим один компонент в шаблоне другого
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-inner-component.html
summary: false
toc: false
---

### Задача

Вывести компонент в шаблоне другого компонента, при условии включенного кэширования Битрикс.

### Решение

Основная проблема вложенных компонентов заключается в том, что при включенном кэшировании код вложенных компонентов выполняться не будет и соответственно актуализации данных не произойдет без очистки кэша основного компонента.

Для решения этой проблемы можно воспользоватья файлом component_epilog.php

* **Добавим метку в шаблон основного компонента (template.php)**

  ```php
  <?if(!defined("B_PROLOG_INCLUDED") || B_PROLOG_INCLUDED!==true)die();
  $this->setFrameMode(true);
  // начинаем буферизацию вывода
  ob_start();
  // тут разметка и код основного компонента, а в нужном месте вставляем метку:
  
  #INNER_BLOCK_1#
  
  <?
  // передаем данные буфера вывода в файл component_epilog.php
  $this->__component->SetResultCacheKeys(array("CACHED_TPL"));
  $this->__component->arResult["CACHED_TPL"] = @ob_get_contents();
  ob_get_clean();
  ?>
  ```
  
* **Вынесем в файл component_epilog.php основного компонента вызов вложенного компонента и всей нужной разметки**

  ```php
  <? require($_SERVER["DOCUMENT_ROOT"] . "/bitrix/header.php");
  /**
   * @var CMain $APPLICATION
   * @var array $arResult
   */
  
  // callback function
  $replacer = function ($matches) use ($APPLICATION) {
    ob_start();
    // тут вставляем разменту, вызовы компонентов, в общем все что нужно вывести
    // в метке #INNER_BLOCK_123# мы можем передать в качестве числа например код инфоблока
    // и использовать его так :
    $id = $matches[1];
    // например в вызове компонента списка новостей:
    $APPLICATION->IncludeComponent(
      "bitrix:news.list",
      "article-list",
      Array(
        'IBLOCK_ID' => $id,
        // тут миллион параметров компонента
      )
    );
    
    return ob_get_clean();
  };

  // находим метку и заменяем ее на результат работы нашей функции
  echo preg_replace_callback(
    "/#INNER_BLOCK_([\\d]+)#/is" . BX_UTF_PCRE_MODIFIER,
    $replacer,
    $arResult["CACHED_TPL"]
  );
  ```
