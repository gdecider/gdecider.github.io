---
title: Front-end Настройка среды
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_front-setup.html
summary: false
toc: false
---

## Настройка среды 

### Установка node.js и npm
[источник](https://github.com/nodesource/distributions#installation-instructions)

10.x - меняем на нужную версию (Пример: 4.x, 8.x, 10.x)

``` bash
cd ~
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install -y build-essential
```

### [Установка yarn](https://yarnpkg.com/lang/en/docs/install/)

```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt-get update && sudo apt-get install yarn
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
yarn --version
```

## Если нужно удалить

```
npm uninstall -g gulp 
npm uninstall -g bower 
sudo apt-get purge --auto-remove yarn
sudo apt-get purge --auto-remove nodejs
```

## Получение и настройка шаблона для верстки

{% include note.html content="Список deprecated плагинов для gulp и их альтернатива 
https://github.com/gulpjs/plugins/blob/master/src/blackList.json" %}

```
# получаем репозиторий
cd <youre project path>
git clone https://github.com/gdecider/fe-startTmpl.git .

# ИЛИ
# git clone git@github.com:gdecider/fe-startTmpl.git .

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
yarn init
```

* ставим все нужные зависимости

```
yarn  browser-sync
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
      gulp-autoprefixer
	  gulp-plumber 
	  gulp-notify 
	  gulp-connect-php --dev
```

* Создадим файл для инструкций галп

```
touch gulpfile.js
```

```js
'use strict';

var gulp        = require('gulp');
var sass        = require('gulp-sass');
var browserSync = require('browser-sync').create();
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
var plumber     = require('gulp-plumber');
var notify      = require('gulp-notify');
var connectPHP  = require('gulp-connect-php');
var bsreload    = browserSync.reload;

var srcPath = './src';
var distPath = './dist';
var vendorsPath = './node_modules';

// === NEW
var path = {
	html: {
		src: [srcPath + '/**/*.html', srcPath + '/**/*.php'],
		dest: srcPath,
		dist: distPath
	},
	sass: {
		src: [srcPath + '/sass/**/*.+(sass|scss)'],
		dest: srcPath + '/css',
		dist: distPath + '/css'
	},
	css: {
		src: [srcPath + '/css/**/*.css'],
		dest: srcPath + '/css',
		dist: distPath + '/css'
	},
	js: {
		src: [srcPath + '/js/**/*'],
		dest: srcPath + '/js',
		dist: distPath + '/js'
	},
	img: {
		src: [srcPath + '/img/**/*'],
		dest: srcPath + '/img',
		dist: distPath + '/img'
	},
	font: {
		src: [srcPath + '/fonts/**/*'],
		dest: srcPath + '/fonts',
		dist: distPath + '/fonts'
	},
	vendor: {
		js: [
			vendorsPath + '/jquery/dist/jquery.min.js',
		],
		css: [
			//vendorsPath + '/vendor-name/path-to/some.css',
		],
		jsFileName: 'vendors.min.js',
		cssFileName: 'vendors.min.css',
	}
};

// ф-я для перехвата ошибок
var onError = function (err) {
    notify({
		title: 'Gulp Task Error',
		message: '!!!ERROR!!! Check the console.'
	}).write(err);

	console.log(err.toString());

	this.emit('end');
}

// WATCHER
gulp.task('watch', ['pre-build'], function(){
	gulp.watch(path.sass.src, ['assets:sass']);
	gulp.watch(path.html.src, ['assets:html']);
	gulp.watch(path.js.src, ['assets:js']);
});

// SYNC HTML (no php)
gulp.task('sync:html', ['watch'], function () {
    browserSync.init({
        server: {
            baseDir: srcPath
        },
        notify: false,
        open: true
    });
});

// SYNC ALL HTML + PHP
gulp.task('sync', ['watch'], function () {
    connectPHP.server({base: srcPath, keepalive: true, hostname: 'localhost', port: 8888, open: false});
    browserSync.init({
        proxy: '127.0.0.1:8888',
        notify: false,
        open: true
    });
});

// The default task (called when you run `gulp` from cli)
gulp.task('default', ['build']);

// TASK pre-build
gulp.task('pre-build', [
	'vendor:clean', 'vendor:js', 'vendor:css',
	'assets:sass', 'assets:js',
]);

// TASK build
gulp.task('build', ['dist:clean', 'pre-build', 'assets:img'], function(){
	var buildCss = gulp.src(path.css.src)
	.pipe(gulp.dest(path.css.dist));

	var buildFonts = gulp.src(path.font.src)
	.pipe(gulp.dest(path.font.dist));

	var buildJs = gulp.src(path.js.src)
	.pipe(gulp.dest(path.js.dist));

	var buildHtml = gulp.src(path.html.src)
	.pipe(gulp.dest(path.html.dist));
});

// TASK clean build dir
gulp.task('dist:clean', function(){
	return del.sync([
		distPath
	]);
});

// TASK clear cache
gulp.task('clear', function(){
	return cache.clearAll();
});

// TASK vendor:clean
gulp.task('vendor:clean', function(){
	return del.sync([
		path.css.dest + '/' + path.vendor.cssFileName,
		path.js.dest + '/' + path.vendor.jsFileName,
	]);
});

// TASK vendor:js
gulp.task('vendor:js', function(){
	return gulp.src(path.vendor.js)
		.pipe(concat(path.vendor.jsFileName))
		.pipe(uglify())
		.pipe(gulp.dest(path.js.dest));
		//.pipe(bsreload({stream: true}));
});

// TASK vendor:css
gulp.task('vendor:css', function(){
	return gulp.src(path.vendor.css)
	.pipe(sourcemaps.init())
	.pipe(concat(path.vendor.cssFileName))
	.pipe(cleanCss())
	.pipe(sourcemaps.write())
	.pipe(gulp.dest(path.css.dest));
});

// TASK assets:sass
gulp.task('assets:sass', function(){
	return gulp.src(path.sass.src)
		.pipe(plumber({ errorHandle: onError }))
		.pipe(sass())
		.on('error', onError)
		.pipe(autoprefixer(['last 15 versions', '> 1%', 'ie 8', 'ie 7'], {cascade: true}))
		.pipe(gulp.dest(path.sass.dest))
		.pipe(notify({
	        title   : 'Gulp Task Complete',
	        message : 'Styles have been compiled'
	    }))
		.pipe(bsreload({stream: true}));
});

// TASK assets:html
gulp.task('assets:html', function(){
	return gulp.src(path.html.src)
		.pipe(bsreload({stream: true}));
});

// TASK assets:js
gulp.task('assets:js', function(){
	return gulp.src(path.js.src)
		.pipe(bsreload({stream: true}));
});

// TASK assets:img minify
gulp.task('assets:img', function(){
	return gulp.src(path.img.src)
	.pipe(cache(imagemin({
		interlaced: true,
		progressive: true,
		svgoPluggins: [{removeViewBox: false}],
		use: [pngquant()]
	})))
	.pipe(gulp.dest(path.img.dist));
});

// === OLD

// uglify css libs
// gulp.task('minify-css', ['sass'], function(){
// 	return gulp.src([
// 		srcPath + '/css/**/*.css',
// 		'!'+srcPath + '/css/**/*.min.css',
// 	])
// 	.pipe(sourcemaps.init())
// 	.pipe(cleanCss())
// 	.pipe(sourcemaps.write())
// 	.pipe(rename({suffix: '.min'}))
// 	.pipe(gulp.dest(srcPath + '/css'));
// });
```

## Yarn комманды

* Добавление зависимости
  
  ```bash
  yarn add [package] [--dev/-D]
  yarn add [package]@[version] [--dev/-D]
  yarn add [package]@[tag] [--dev/-D]
  ```

* Обновление зависимости
  
  ```bash
  yarn upgrade [package]
  yarn upgrade [package]@[version]
  yarn upgrade [package]@[tag]
  ```

* Удаление зависимости
  
  ```bash
  yarn remove [package]
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
