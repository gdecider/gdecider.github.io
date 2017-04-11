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