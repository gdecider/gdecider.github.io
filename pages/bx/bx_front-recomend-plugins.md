---
title: Front-end Рекомендуемые плагины
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_front-recomend-plugins.html
summary: false
toc: false
---

## Инструменты

* Pixel Perfect браузерное дополнение для наложения png на живую верстку

  * [Plugin FireFox](https://addons.mozilla.org/ru/firefox/addon/pixel-perfect/)

* Avocode кросплатформенное приложение для удобного получения информации о размерах блоков, шрифтов, и прочих нюансов верстки (ПЛАТНЫЙ)

  * [https://avocode.com/](https://avocode.com/)

## Web-сервисы
* https://www.bertal.ru/ - проверка ответов страниц, редиректов и т.п.
  * [https://www.bertal.ru/](https://www.bertal.ru/)

* Transfonter - преобразование шрифтов для подключения на сайт
  * [https://transfonter.org/](https://transfonter.org/)
  
* loading.io - создание прелоадеров (svg, gif, png)  
  * [https://loading.io/](https://loading.io/)
  
* Конвертер видео в OGV и WEBM
  * [https://www.online-convert.com/ru/#](https://www.online-convert.com/ru/#)
  
* kto-dostavit.ru - расчет стоимости доставки товара
  * [https://kto-dostavit.ru/](https://kto-dostavit.ru/)
  
* svgsprit - создание svg спрайтов
  * [https://svgsprit.es/](https://svgsprit.es/)
  
## CSS Grids

* Bootstrap 3 Grid SASS [Link to npmjs](https://www.npmjs.com/package/bootstrap-sass-grid)
  
  ```sh
  yarn add bootstrap-sass-grid
  ```
  
* Bootstrap 4 Grid SASS [Link to npmjs](https://www.npmjs.com/package/bootstrap-4-grid)
  
  ```sh
  yarn add bootstrap-4-grid
  ```

## Рекомендуемые плагины, библиотеки и дополнения

### Иконочные шрифты

* Font awesome
  * [https://fontawesome.com](https://fontawesome.com)
 
  **Получение**
 
  ```bash
  yarn add --dev @fortawesome/fontawesome-free
  ```
  
  **Пути для подключения**
  
  ```
  css: [
  	vendorsPath + '/@fortawesome/fontawesome-free/css/all.min.css',
  ],  
  ```
  
  **Скопировать папку со шрифтами ```/node_modules/@fortawesome/fontawesome-free/webfonts``` в ```/src/```**
  
### Слайдеры

* Slick slider

  * [http://kenwheeler.github.io/slick/](http://kenwheeler.github.io/slick/)

  * [https://github.com/kenwheeler/slick/](https://github.com/kenwheeler/slick/)
  
  **Получение плагина**
  
  ```bash
  yarn add slick-carousel
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/slick-carousel/slick/slick.js',
  ],
  css: [
  	vendorsPath + '/slick-carousel/slick/slick.css',
  ],
  ```

* BXSlider

  * [http://bxslider.com/](http://bxslider.com/)

* Owl carousel 2

  * [https://owlcarousel2.github.io/OwlCarousel2/index.html](https://owlcarousel2.github.io/OwlCarousel2/index.html)

  * [https://github.com/OwlCarousel2/OwlCarousel2](https://github.com/OwlCarousel2/OwlCarousel2)

* Jssor slider (возможность настраивать сложные эффекты)

  * [https://www.jssor.com/](https://www.jssor.com/)

* Camera (интересные эффекты переключения слайдов)

  * [http://www.pixedelic.com/plugins/camera/](http://www.pixedelic.com/plugins/camera/)

### Счетчики

* FlipClock.js
  * [http://flipclockjs.com/](http://flipclockjs.com/)
  * [https://github.com/objectivehtml/FlipClock](https://github.com/objectivehtml/FlipClock)

### Parallax

* Parallax.js
 
  * [https://github.com/wagerfield/parallax](https://github.com/wagerfield/parallax)

* Simple Parallax Scrolling

  * [http://pixelcog.github.io/parallax.js/](http://pixelcog.github.io/parallax.js/)

  * [https://github.com/pixelcog/parallax.js/](https://github.com/pixelcog/parallax.js/)

* Stellar.js

  * [http://markdalgleish.com/projects/stellar.js/docs/](http://markdalgleish.com/projects/stellar.js/docs/)

* jQuery Parallax

  * [http://ianlunn.co.uk/plugins/jquery-parallax/](http://ianlunn.co.uk/plugins/jquery-parallax/)
  
  * [https://github.com/IanLunn/jQuery-Parallax](https://github.com/IanLunn/jQuery-Parallax)
  
### Полноэкранная прокрутка

* One Page Scroll

  * [https://github.com/peachananr/onepage-scroll](https://github.com/peachananr/onepage-scroll)

* fullPage.js

  * [http://alvarotrigo.com/fullPage/](http://alvarotrigo.com/fullPage/)

  * [https://github.com/alvarotrigo/fullPage.js](https://github.com/alvarotrigo/fullPage.js)

* pagePiling.js

  * [http://alvarotrigo.com/pagePiling/](http://alvarotrigo.com/pagePiling/)

  * [https://github.com/alvarotrigo/pagePiling.js](https://github.com/alvarotrigo/pagePiling.js)

* multiScroll.js

  * [http://alvarotrigo.com/multiScroll/](http://alvarotrigo.com/multiScroll/)

  * [https://github.com/alvarotrigo/multiscroll.js](https://github.com/alvarotrigo/multiscroll.js)

* SCROLLIFY

  * [https://projects.lukehaas.me/scrollify/](https://projects.lukehaas.me/scrollify/)

  * [https://github.com/lukehaas/Scrollify](https://github.com/lukehaas/Scrollify)

### Горизонтальная прокрутка (вместо вертикальной)

* jInvertScroll

  * [http://www.pixxelfactory.net/jInvertScroll/](http://www.pixxelfactory.net/jInvertScroll/)

  * [https://github.com/pixxelfactory/jInvertScroll](https://github.com/pixxelfactory/jInvertScroll)

### Вариативное поведение элементов при прокрутке

* jQuery Scroll Path

  * [http://joelb.me/scrollpath/](http://joelb.me/scrollpath/)

  * [https://github.com/JoelBesada/scrollpath](https://github.com/JoelBesada/scrollpath)

### Трансформация текста

* funnyText.js

  * [http://alvarotrigo.com/funnyText/](http://alvarotrigo.com/funnyText/)

  * [https://github.com/alvarotrigo/funnyText.js](https://github.com/alvarotrigo/funnyText.js)

### Всплывающие окна

* FancyBox 3
  
  * [http://fancyapps.com/fancybox/3/](http://fancyapps.com/fancybox/3/)

  * [https://github.com/fancyapps/fancybox](https://github.com/fancyapps/fancybox)
  
  **Получение плагина**
  
  ```bash
  yarn add @fancyapps/fancybox
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/@fancyapps/fancybox/dist/jquery.fancybox.js',
  ],
  css: [
  	vendorsPath + '/@fancyapps/fancybox/dist/jquery.fancybox.css',
  ],
  ```

* Magnific Popup

  * [http://dimsemenov.com/plugins/magnific-popup/](http://dimsemenov.com/plugins/magnific-popup/)

  * [https://github.com/dimsemenov/Magnific-Popup](https://github.com/dimsemenov/Magnific-Popup)
  
### Всплывающие подсказки (tooltips)

* popper.js

  * [https://popper.js.org/](https://popper.js.org/)
  
  * [https://github.com/FezVrasta/popper.js](https://github.com/FezVrasta/popper.js)

### Изменение страницы при прокрутке

* HC-Sticky

  * [https://github.com/somewebmedia/hc-sticky](https://github.com/somewebmedia/hc-sticky)
  
  **Получение плагина**
  
  ```bash
  yarn add hc-sticky
  ```
  
  **Пути для подключения**
  
  ```
  js: [
 	vendorsPath + '/hc-sticky/dist/hc-sticky.js',
  ],
  ```

* sticky-header 

  Используется для простого закрепления меню/шапки при прокрутке
  
  * [https://www.npmjs.com/package/sticky-header](https://www.npmjs.com/package/sticky-header)
  * [https://github.com/ltebean/sticky-header](https://github.com/ltebean/sticky-header)
  
  **Получение плагина**
  
  ```bash
  yarn add sticky-header
  ```
  
  **Пути для подключения**
  
  ```
  js: [
 	vendorsPath + '/sticky-header/index.js',
  ],
  ```

* Sticky-Kit

  Закрепление меню, нескольких меню при прокрутке
  
  * [http://leafo.net/sticky-kit/](http://leafo.net/sticky-kit/)
  * [https://github.com/leafo/sticky-kit](https://github.com/leafo/sticky-kit)

* jQuery.Scroolly

  * [https://github.com/chayka/jQuery.Scroolly](https://github.com/chayka/jQuery.Scroolly)
  
* Wow

  * [http://mynameismatthieu.com/WOW/](http://mynameismatthieu.com/WOW/)

* ScrollMagic

  * [http://scrollmagic.io/](http://scrollmagic.io/)

  * [https://github.com/janpaepke/ScrollMagic](https://github.com/janpaepke/ScrollMagic)

### Анимированная прокрутка страницы

* AnimateScroll

  * [http://plugins.compzets.com/animatescroll/](http://plugins.compzets.com/animatescroll/)

  * [https://github.com/ramswaroop/animatescroll.js](https://github.com/ramswaroop/animatescroll.js)

### Анимированная отрисовка линий

* Lazy Line Painter

  * [http://lazylinepainter.info/](http://lazylinepainter.info/)

  * [https://github.com/camoconnell/lazy-line-painter](https://github.com/camoconnell/lazy-line-painter)

### Прокрутка к элементу страницы

* jquery-smooth-scroll 

  * [https://www.npmjs.com/package/jquery-smooth-scroll](https://www.npmjs.com/package/jquery-smooth-scroll)

  ```
  yarn add jquery-smooth-scroll
  ```
  
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/jquery-smooth-scroll/jquery.smooth-scroll.js',
  ],
  ```

### Валидаторы

* jQuery Validation Plugin
  
  Валидация полей формы

  * [https://jqueryvalidation.org/](https://jqueryvalidation.org/)
  
  **Получение плагина**
  
  ```bash
  yarn add jquery-validation
  ```
  
  **Пути для подключения**
  
  ```
  js: [
    vendorsPath + '/jquery-validation/dist/jquery.validate.js',
    vendorsPath + '/jquery-validation/dist/localization/messages_ru.js',
  ],
  ```

* validPic.js
  
  Валидация файлов изображений на основе mimetypes

  * [https://github.com/alvarotrigo/validPic.js](https://github.com/alvarotrigo/validPic.js)
  
  **Получение плагина**
  
  ```bash
  yarn add validpic.js
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '',
  ],
  ```
### Работа с буфером обмена

* clipboard.js

  * [https://clipboardjs.com/](https://clipboardjs.com/)
  
  **Получение плагина**
  
  ```bash
  yarn add clipboard
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '...',
  ],
  ```
  
### Кастомизация элементов

#### Маски ввода

* Masked Input Plugin for jQuery
  
  * [http://digitalbush.com/projects/masked-input-plugin/](http://digitalbush.com/projects/masked-input-plugin/)

  * [https://github.com/digitalBush/jquery.maskedinput](https://github.com/digitalBush/jquery.maskedinput)
  
  **Получение плагина**
  
  ```bash
  yarn add jquery.maskedinput
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/jquery.maskedinput/src/jquery.maskedinput.js',
  ],
  ```

#### Выпадающие списки

* jquery-custom-select
  * [https://kvlsrg.github.io/jquery-custom-select/](https://kvlsrg.github.io/jquery-custom-select/)
  
  **Получение плагина**
  
  ```bash
  yarn add jquery-custom-select
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/jquery-custom-select/dist/js/jquery.custom-select.js',
  ],
  css: [
  	vendorsPath + '/jquery-custom-select/dist/css/jquery.custom-select.css',
  ],
  ```

* jQuery Selectric

  * [http://selectric.js.org/](http://selectric.js.org/)

  * [https://github.com/lcdsantos/jQuery-Selectric](https://github.com/lcdsantos/jQuery-Selectric)
  
  **Получение плагина**
  
  ```bash
  yarn add selectric
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/selectric/src/jquery.selectric.js',
  ],
  sass: [
  	vendorsPath + '/selectric/src/selectric.scss',
  ],
  ```
* jQuery Nice Select

  * [http://hernansartorio.com/jquery-nice-select/](http://hernansartorio.com/jquery-nice-select/)
  
  * [https://github.com/hernansartorio/jquery-nice-select](https://github.com/hernansartorio/jquery-nice-select)
  
  **Получение плагина**
  
  ```bash
  yarn add jquery-nice-select
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/jquery-nice-select/js/jquery.nice-select.js',
  ],
  sass: [
  	vendorsPath + '/jquery-nice-select/scss/style.scss',
  ],
  ```

* Minimalect

  * [http://groenroos.github.io/minimalect/](http://groenroos.github.io/minimalect/)

  * [https://github.com/groenroos/minimalect](https://github.com/groenroos/minimalect)

#### Рейтинг звезды

* jQuery Bar Rating

  * [http://antenna.io/demo/jquery-bar-rating/examples/](http://antenna.io/demo/jquery-bar-rating/examples/)

  * [https://github.com/antennaio/jquery-bar-rating](https://github.com/antennaio/jquery-bar-rating)

#### Scrollbar

* jQuery custom content scroller

  * [http://manos.malihu.gr/jquery-custom-content-scroller/](http://manos.malihu.gr/jquery-custom-content-scroller/)
  
  * [https://github.com/malihu/malihu-custom-scrollbar-plugin](https://github.com/malihu/malihu-custom-scrollbar-plugin)
  
  **Получение плагина**
  
  ```bash
  yarn add malihu-custom-scrollbar-plugin
  ```
  
  **Пути для подключения**
  
  ```
  js: [
	vendorsPath + '/malihu-custom-scrollbar-plugin/jquery.mCustomScrollbar.js',
  ],
  css: [
  	vendorsPath + '/malihu-custom-scrollbar-plugin/jquery.mCustomScrollbar.css',
  ],
  ```

### Фиксация элементов

* HC-Sticky – jQuery Floating Sticky Plugin

  * [http://someweblog.com/hcsticky-jquery-floating-sticky-plugin/](http://someweblog.com/hcsticky-jquery-floating-sticky-plugin/)

  * [https://github.com/somewebmedia/hc-sticky](https://github.com/somewebmedia/hc-sticky)
  
### Zoom при наведении

* xZoom

  * [https://github.com/payalord/xZoom](https://github.com/payalord/xZoom)
  
* jQuery Zoom

  * [http://www.jacklmoore.com/zoom/](http://www.jacklmoore.com/zoom/)
  
  * [https://github.com/jackmoore/zoom](https://github.com/jackmoore/zoom)
  
### Элементы интерфейса

#### Меню

* mmenu (выезд с боку со сдвигом контента)
	
  * [http://mmenu.frebsite.nl/](http://mmenu.frebsite.nl/)

  * [https://github.com/FrDH/jQuery.mmenu](https://github.com/FrDH/jQuery.mmenu)

#### Кнопка "гамбургер"

* hamburgers (разные эффекты анимации)

  * [https://jonsuh.com/hamburgers/](https://jonsuh.com/hamburgers/)

  * [https://github.com/jonsuh/hamburgers](https://github.com/jonsuh/hamburgers)

#### Табы (адаптивные)

* jQuery Responsive Tabs

  * [http://jellekralt.github.io/Responsive-Tabs/](http://jellekralt.github.io/Responsive-Tabs/)

  * [https://github.com/jellekralt/Responsive-Tabs](https://github.com/jellekralt/Responsive-Tabs)

### Графики

* Chart.js

  * [http://www.chartjs.org/](http://www.chartjs.org/)

  * [https://github.com/chartjs/Chart.js](https://github.com/chartjs/Chart.js)

* D3.js

  * [https://github.com/d3/d3/wiki/Gallery](https://github.com/d3/d3/wiki/Gallery)

* highcharts

  * [https://www.highcharts.com/](https://www.highcharts.com/)

  * [https://github.com/highcharts/highcharts](https://github.com/highcharts/highcharts)

### Аудио и видео плееры

* MediaElement.js

  * [http://www.mediaelementjs.com/](http://www.mediaelementjs.com/)
  
### Прочее

* Holder.js - создание paceholders картинок на стороне клиента.

  * [https://github.com/imsky/holder](https://github.com/imsky/holder)
  
## Требующие рассмотрения

* [https://push.world](https://push.world)
