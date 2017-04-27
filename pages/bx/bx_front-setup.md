---
title: Front-end Настройка среды
keywords: sample homepage
sidebar: bx_sidebar
permalink: bx_front-setup.html
summary: false
toc: false
---

## Настройка среды 

### Установка node.js и npm

[источник](https://www.digitalocean.com/community/tutorials/node-js-ubuntu-16-04-ru)

7.x - меняем на нужную версию (Пример: 4.x, 6.x, 7.x)

``` bash
cd ~
curl -sL https://deb.nodesource.com/setup_7.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt-get install nodejs
sudo apt-get install build-essential
```

### Установка bower

```
sudo npm i -g bower
```

### Установка gulp

```
sudo npm i -g gulp
```

### Проверка версий

```
node -v
npm -v
gulp -v
bower -v
```

## Если нужно удалить

```
npm uninstall -g gulp 
npm uninstall -g bower 
sudo apt-get remove --purge node
```

## Получение и настройка шаблона для верстки

{% include note.html content="Список deprecated плагинов для gulp и их альтернатива 
https://github.com/gulpjs/plugins/blob/master/src/blackList.json" %}

```
# получаем репозиторий
cd <youre project path>
git clone git@github.com:gdecider/fe-startTmpl.git .

# устанавливаем зависимости
npm i
bower i

# стартуем вотчер и верстаем
gulp watch
```

## Настройка шаблона для верстки в ручную

* Перейти в папку проекта

```bash
cd <youre project path>
```

* инициализируем

```
npm init
```

* ставим все нужные зависимости

```
npm i browser-sync
      del
      gulp
      gulp-sass
      gulp-concat 
      gulp-uglify
      gulp-clean-css
      gulp-sourcemaps 
      gulp-rename      
      gulp-imagemin 
      imagemin-pngquant
      gulp-cache
      gulp-autoprefixer --save-dev
```
* Создадим файл для инструкций галп

```
touch gulpfile.js
```

```js
'use strict';

var gulp        = require('gulp');
var sass        = require('gulp-sass');
var browserSync = require('browser-sync');
var concat      = require('gulp-concat');
var uglify      = require('gulp-uglify');
var cleanCss    = require('gulp-clean-css');
var rename      = require('gulp-rename');
var del         = require('del');
var imagemin    = require('gulp-imagemin');
var pngquant    = require('imagemin-pngquant');
var cache       = require('gulp-cache');
var autoprefixer= require('gulp-autoprefixer');
var sourcemaps  = require('gulp-sourcemaps');

// обработка sass
gulp.task('sass', function(){
	return gulp.src('./app/sass/**/*.+(sass|scss)')
			.pipe(sass())
			.pipe(autoprefixer(['last 15 versions', '> 1%', 'ie 8', 'ie 7'], {cascade: true}))
			.pipe(gulp.dest('app/css'))
			.pipe(browserSync.reload({stream: true}));
});

// uglify
gulp.task('scripts', function(){
	return gulp.src([
		'app/libs/jquery/dist/jquery.min.js',		
	])
	.pipe(concat('libs.min.js'))
	.pipe(uglify())
	.pipe(gulp.dest('app/js'));
});

// uglify css libs
gulp.task('minify-css', ['sass'], function(){
	return gulp.src([
		'app/css/**/*.css',		
	])
	.pipe(sourcemaps.init())
	.pipe(cleanCss())
	.pipe(sourcemaps.write())
	.pipe(rename({suffix: '.min'}))
	.pipe(gulp.dest('app/css'));
});

// browser-sync
gulp.task('sync', function(){
	browserSync({
		server: {
			baseDir: 'app',
		},
		notify: false
	});
});

// minify img
gulp.task('img', function(){
	return gulp.src('app/img/**/*')
	.pipe(cache(imagemin({
		interlaced: true,
		progressive: true,
		svgoPluggins: [{removeViewBox: false}],
		use: [pngquant()]
	})))
	.pipe(gulp.dest('dist/img'));
});

// clean build dir
gulp.task('clean', function(){
	return del.sync('dist');
});

// clear cache
gulp.task('clear', function(){
	return cache.clearAll();
});

// watcher
gulp.task('watch', ['sync', 'minify-css', 'scripts'], function(){
	gulp.watch('./app/sass/**/*.+(sass|scss)', ['sass']);
	gulp.watch('app/*.html', browserSync.reload);
	gulp.watch('app/js/**/*.js', browserSync.reload);
});

// build
gulp.task('build', ['clean', 'minify-css', 'scripts', 'img'], function(){
	var buildCss = gulp.src([
		'app/css/main.css',
		'app/css/libs.min.css',
	])
	.pipe(gulp.dest('dist/css'));

	var buildFonts = gulp.src([
		'app/fonts/**/*',
	])
	.pipe(gulp.dest('dist/fonts'));

	var buildJs = gulp.src([
		'app/js/**/*',
	])
	.pipe(gulp.dest('dist/js'));

	var buildHtml = gulp.src([
		'app/*.html',
	])
	.pipe(gulp.dest('dist'));
});
```

* bower создаем файл .bowerrc в корне проекта

```
{
	"directory": "app/libs/"
}
```

## Bower комманды

* Создание манифеста

```
bower init
```

* Просмотр списка установленных пакетов

```
bower list
bower list --path
```

* Поиск пакета

```
bower s <name>
```

* Просмотр информации о пакете

```
bower info <name>
```

* Установка пакета

```
bower i <name> --save
bower i <name>#<version> --save-dev
```

* Обновление пакета

```
bower update <name>
```

* Удаление пакета

```
bower uninstall <name> --save
```