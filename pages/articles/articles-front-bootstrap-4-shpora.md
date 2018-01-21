---
title: Шпаргалка Bootstrap 4 | Статьи Front-end
keywords: Шпаргалка Bootstrap 4
sidebar: articles_sidebar
folder: articles
permalink: articles-front-bootstrap-4-shpora.html
summary: false
toc: false
---

## Полезные статьи

[Bootstrap 4 - Классы для работы с flexbox](https://itchief.ru/sections/bootstrap/classes-for-working-with-flexbox)

### Выравнивание элементов по ширине с переносом в случае сужения страницы

```html
<div class="d-flex flex-wrap justify-content-between">
    <div class="item">some item 1</div>
    <div class="item">some item 2</div>
    <div class="item">some item 3</div>
    <div class="item">some item 4</div>
    <div class="item">some item 5</div>
</div>
```

**Варианты**

```html
justify-content-start
justify-content-end
justify-content-center
justify-content-between
justify-content-around
```

**Классы для разных размеров экранов**

```html
justify-content-<size>-<type>
```


### Вертикальное выравнивание элементов по центру

```html
<div class="d-flex align-items-center">
    <div class="item">some item 1</div>
    <div class="item">some item 2</div>
    <div class="item">some item 3</div>
    <div class="item">some item 4</div>
    <div class="item">some item 5</div>
</div>
```

**Варианты**

```html
align-items-start
align-items-end
align-items-center
align-items-baseline
align-items-stretch
```

**Классы для разных размеров экранов**

```html
align-items-<size>-<type>
```
