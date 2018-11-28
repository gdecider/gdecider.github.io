---
title: Back-end Code Style
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_back-code-style.html
summary: false
toc: false
---

## Стиль написания кода

Основное правило - код проекта должен выглядеть так, будто его писал один человек.

### Настройка IDE

Для PHPStorm установить плагин [https://plugins.jetbrains.com/plugin/7294-editorconfig](https://plugins.jetbrains.com/plugin/7294-editorconfig).

Добавить в корень проекта конфиг файл ```.editorconfig```:

```
root = true

[*]
indent_style = space
indent_size = 4
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
```

### Командные стандарты



### Общие стандарты

* [http://www.php-fig.org/](http://www.php-fig.org/)

* [PSR-1: Basic Coding Standard](http://www.php-fig.org/psr/psr-1/)

* [PSR-2: Coding Style Guide](http://www.php-fig.org/psr/psr-2/)

* [PSR-3: Logger Interface](http://www.php-fig.org/psr/psr-3/)

* [PSR-4: Autoloader](http://www.php-fig.org/psr/psr-4/)

* [PSR-6: # Introduction](http://www.php-fig.org/psr/psr-6/)

* [PSR-7: HTTP message interfaces](http://www.php-fig.org/psr/psr-7/)

### Договеренности

#### Битрикс

* Все данные, добавляемые в массив ```$arResult``` при кастомизации стандартных компонентов писать в ```$arResult['CSTM']``` CSTM сокращение от custom.

* Именовать константы в соответствии с [правилами](https://gdecider.github.io/bx_struktura-proekta.html#localphp_interfaceincludeconstantsphp)

* Имена глобальных переменных, используемых в проектах:

  * Фильтр в комплексном компоненте каталога: ```arrFilterCatalog```
  
### Полезные статьи

* [5 PHP Coding Standards You Will Love and How to Use them](https://blog.sideci.com/5-php-coding-standards-you-will-love-and-how-to-use-them-adf6a4855696)
