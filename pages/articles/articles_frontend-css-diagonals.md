---
title: Статьи CSS | Диагональные элементы
keywords: sample homepage
sidebar: articles_sidebar
permalink: articles_frontend-css-diagonals.html
summary: false
toc: false
---

## Диагональные элементы дизайна при помощи CSS

### Задача

Сверстать треугольник.

### Решение 1. Применение border.
    
  Диагональ создается за счет окрашивания границ в одинаковые цвета

  * Не прозрачные треугольники

    ```css
    .triangle {
        width: 0;
        height: 0;
        border-top: 50px solid #af0000;
        border-right: 50px solid #00af00;
        border-bottom: 50px solid #0000af;
        border-left: 50px solid #af00af;
    }
    ```

    <script async src="//jsfiddle.net/Decider/ndn6jo9f/embed/result,css,html/"></script>

  * Прозрачные треугольники

    ```css
    .triangle {
        width: 0;
        height: 0;
        border-top: 50px solid rgba(150, 0, 0, 0.8);
        border-right: 50px solid rgba(0, 150, 0, 0.8);
        border-bottom: 50px solid rgba(0, 0, 150, 0.8);
        border-left: 50px solid rgba(150, 0, 150, 0.8);
    }
    ```

    <script async src="//jsfiddle.net/Decider/we1t020q/embed/result,css,html/"></script>

**Плюсы:**

  * Можно верстать не только треугольники, но и более сложные фигуры

  * Хорошая поддержка в браузерах

**Минусы:**

  * фиксированная высота и ширина, что не подойдет для блоков с изменяющимся текстом

### Решение 2. Применение linear-gradient.

  * Прозрачные треугольники НИЗ-ВЕРХ
    
    ```css
    .triangle {
        height: 100px;
        width: 100px;
        background: linear-gradient(to bottom right, rgba(150, 0, 0, 0.8) 0%, rgba(150, 0, 0, 0.8) 50%, rgba(0, 150, 0, 0.8) 50%, rgba(0, 150, 0, 0.8) 100%);
    }
    ```

    <script async src="//jsfiddle.net/Decider/pLjzbgm8/embed/result,css,html/"></script>
  
  * Прозрачные треугольники ВЕРХ-НИЗ
    
    ```css
    .triangle {
        height: 100px;
        width: 100px;
        background: linear-gradient(to top right, rgba(150, 0, 0, 0.8) 0%, rgba(150, 0, 0, 0.8) 50%, rgba(0, 150, 0, 0.8) 50%, rgba(0, 150, 0, 0.8) 100%);
    }
    ```

    <script async src="//jsfiddle.net/Decider/1sxf6wc1/embed/result,css,html/"></script>

**Плюсы:**

  * Можно выставить высоту родителя и применять к блокам с изменяющимися размерами.