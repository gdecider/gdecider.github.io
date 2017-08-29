---
title: Статьи Front-end | Оптимизация
keywords: sample homepage
sidebar: articles_sidebar
folder: articles
permalink: articles_fe-optimization.html
summary: false
toc: false
---

## Оптимизация изображений

```
# JPG
find . -type f -iname '*.jp*g' -print0 | xargs -0 jpegoptim --strip-all --all-progressive --max=85 -t
# PNG
find . -type f -iname '*.png' -print0 | xargs -0 optipng -strip all -o 1 -v
```
