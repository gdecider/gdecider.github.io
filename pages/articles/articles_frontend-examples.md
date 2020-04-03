---
title: Статьи Front-end | Примеры кода
keywords: css js code examples
sidebar: articles_sidebar
folder: articles
permalink: articles_frontend-examples.html
summary: false
toc: false
---

## Примеры кода

### Замена жирного шрифта на тень

Часто в дизайне для выделения объекта при наведении используют увеличение жирности, 
но при этом происходит увеличение ширины букв и текст начинает "скакать",
что бы этого избежать можно вместо изменения жирности добавлять тень, визуально разница удет почти не видна

**Вместо**

```css
.some:hover {
  font-weight: bold;
}
```

**Заменяем на**
```css
.some:hover {
  font-weight: normal;
  text-shadow: 0.25px 0.1px 0px #8c2327, -0.25px -0.1px 0px #8c2327;
}
```
