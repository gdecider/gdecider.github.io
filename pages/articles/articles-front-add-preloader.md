---
title: Добавление прелоадера на сайт | Статьи Front-end
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles-front-add-preloader.html
summary: false
toc: false
---

### Задача

Отобразить прелоадер на сайте до загрузки основного контента.

### Решение

Получить файл прелоадера можно при помощи сервиса [https://loading.io/](https://loading.io/).

Для отображения прелоадера на сайте нужно создать отдельный css и разместить его вверху страницы, что бы он подгружался, как можно раньше. И добавить разметку прелоадера в начало страницы после открывающего body.

Подключение файла стилей:

```html
<link rel="stylesheet" href="css/header.css">
```

Пример файлов стилей:

```scss
.loader {
    background: none repeat scroll 0 0 #ffffff;
    bottom: 0;
    height: 100%;
    left: 0;
    position: fixed;
    right: 0;
    top: 0;
    width: 100%;
    z-index: 9999;
}
.loader_inner {
    background-image: url(../img/page_preloader.svg);
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center center;
    background-color: transparent;
    height: 60px;
    width: 60px;
    margin-top: -30px;
    margin-left: -30px;
    left: 50%;
    top: 50%;
    position: absolute;
}
```

Пример разметки:

```html
<div class="loader">
    <div class="loader_inner"></div>
</div>
```

На событие окончательной загрузки страницы вешаем обработчик скрытия прелоадера:

```js
$(window).load(function () {
    $(".loader_inner").fadeOut();
    $(".loader").delay(100).fadeOut("slow");
});
```

