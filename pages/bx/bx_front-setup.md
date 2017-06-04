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
sudo apt-get remove --purge yarn
sudo apt-get remove --purge node
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
      gulp-autoprefixer
	  gulp-plumber 
	  gulp-notify --save-dev
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
var plumber     = require('gulp-plumber');
var notify      = require("gulp-notify");

var srcPath = './src';
var distPath = './dist';

// ф-я для перехвата ошибок
var onError = function (err) {
    notify({
         title: 'Gulp Task Error',
         message: 'Check the console.'
     }).write(err);

     console.log(err.toString());
     
     this.emit('end');
}

// обработка sass
gulp.task('sass', function(){
	return gulp.src(srcPath + '/sass/**/*.+(sass|scss)')
			.pipe(plumber({ errorHandle: onError }))
			.pipe(sass())
			.on('error', onError)
			.pipe(autoprefixer(['last 15 versions', '> 1%', 'ie 8', 'ie 7'], {cascade: true}))
			.pipe(gulp.dest(srcPath + '/css'))
			.pipe(notify({
		        title   : 'Gulp Task Complete',
		        message : 'Styles have been compiled'
		    }))
			.pipe(browserSync.reload({stream: true}));
});

// uglify
gulp.task('scripts', function(){
	return gulp.src([
		srcPath + '/vendors/jquery/dist/jquery.min.js',		
	])
	.pipe(concat('vendors.min.js'))
	.pipe(uglify())
	.pipe(gulp.dest(srcPath + '/js'));
});

// uglify css libs
gulp.task('minify-css', ['sass'], function(){
	return gulp.src([
		srcPath + '/css/**/*.css',
		'!'+srcPath + '/css/**/*.min.css',		
	])
	.pipe(sourcemaps.init())
	.pipe(cleanCss())
	.pipe(sourcemaps.write())
	.pipe(rename({suffix: '.min'}))
	.pipe(gulp.dest(srcPath + '/css'));
});

// browser-sync
gulp.task('sync', function(){
	browserSync({
		server: {
			baseDir: './src',
		},
		notify: false
	});
});

// minify img
gulp.task('img', function(){
	return gulp.src(srcPath + '/img/**/*')
	.pipe(cache(imagemin({
		interlaced: true,
		progressive: true,
		svgoPluggins: [{removeViewBox: false}],
		use: [pngquant()]
	})))
	.pipe(gulp.dest(distPath + '/img'));
});

// clean build dir
gulp.task('clean', function(){
	return del.sync(distPath);
});

// clear cache
gulp.task('clear', function(){
	return cache.clearAll();
});

// watcher
gulp.task('watch', ['sync', 'minify-css', 'scripts'], function(){
	gulp.watch(srcPath + '/sass/**/*.+(sass|scss)', ['sass']);
	gulp.watch(srcPath + '/**/*.html', browserSync.reload);
	gulp.watch(srcPath + '/js/**/*.js', browserSync.reload);
});

// build
gulp.task('build', ['clean', 'minify-css', 'scripts', 'img'], function(){
	var buildCss = gulp.src([
		srcPath + '/css/**/*.css',
	])
	.pipe(gulp.dest(distPath + '/css'));

	var buildFonts = gulp.src([
		srcPath + '/fonts/**/*',
	])
	.pipe(gulp.dest(distPath + '/fonts'));

	var buildJs = gulp.src([
		srcPath + '/js/**/*',
	])
	.pipe(gulp.dest(distPath + '/js'));

	var buildHtml = gulp.src([
		srcPath + '/**/*.html',
	])
	.pipe(gulp.dest(distPath));
});

// The default task (called when you run `gulp` from cli)
gulp.task('default', ['watch']);
```

* bower создаем файл .bowerrc в корне проекта

```
{
	"directory": "app/libs/"
}
```

## Yarn комманды

* Добавление зависимости
  
  ```bash
  yarn add [package]
  yarn add [package]@[version]
  yarn add [package]@[tag]
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