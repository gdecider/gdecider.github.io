---
title: Адаптивная верстка | Статьи и советы
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_frontend-adaptiv-articles.html
summary: false
toc: true
---

## Работа с изображениями

### Атрибут srcset тега img

```html
<img src="small.jpg" srcset="medium.jpg 1000w, large.jpg 2000w" alt="yah">
```

[Поддержка браузерами](http://caniuse.com/#search=srcset)

Статьи в поддержку:

* [https://www.sitepoint.com/how-to-build-responsive-images-with-srcset/](https://www.sitepoint.com/how-to-build-responsive-images-with-srcset/)
* [https://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/](https://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/)

### Тег picture

```html
<picture>
    <source media="(min-width: 481px)" srcset="/img/920sdffefw.jpg">
    <source srcset="/img/asfasdf320.jpg">
    <!-- img tag for browsers that do not support picture element -->
    <img src="/img/920sdffefw.jpg">
</picture>
```
