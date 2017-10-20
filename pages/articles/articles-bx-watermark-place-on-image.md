---
title: Наложение watermark на изображение | Статьи CMS "1С Битрикс"
keywords: битрикс watermark, масштабировать вотермарк, товар вотермарк битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles-bx-watermark-place-on-image.html
summary: false
toc: false
---

### Задача

Наложить watermark на изображение

### Решение

Функция для наложения watermark

```php
<?php
/**
     * Сжимает изображение и накладывает на него водяной знак.
     * Если водяной знак не найден, просто сжимает изображение.
     *
     * @param $imageId - ID исходного изображения для наложения водяного знака
     * @param $watermarkPath - Путь до изображения водяного знака
     * @param int $width - Целевая ширина конечного изображения
     * @param int $height - Целевая высота конечного изображения
     * @param string $wmPosition - topleft, topcenter, topright, centerleft, center, centerright, bottomleft, bottomcenter, bottomright
     * @param float $wmScale - значение от 0 до 1. Масштаб водяного знака относительно ширины изображения
     * @param int $wmMaxWidth - Максимальная ширина водяного знака в пикселях. Если не установлено, то не учитывается.
     * Необходимо для ограничения ширины водяного знака на больших изображениях
     * @param int $resizeType - Тип масштабирования изображения
     * <p>BX_RESIZE_IMAGE_EXACT - масштабирует в прямоугольник $arSize c сохранением пропорций, обрезая лишнее; </p>
     * <p>X_RESIZE_IMAGE_PROPORTIONAL - масштабирует с сохранением пропорций, размер ограничивается $arSize; </p>
     * <p>BX_RESIZE_IMAGE_PROPORTIONAL_ALT - масштабирует с сохранением пропорций за ширину при этом принимается максимальное значение из высоты/ширины, размер ограничивается $arSize, улучшенная
     * обработка вертикальных картинок.</p>
     * @return mixed - Сжатое изображение с водяным знаком
     */
    function getImageWithWatermark($imageId, $watermarkPath, $width = 320, $height = 320, $wmPosition = 'center', $wmScale = 0.5, $wmMaxWidth,
        $resizeType = BX_RESIZE_IMAGE_PROPORTIONAL)
    {
        $arFilters = [];
        $arDestinationSize = ['width' => $width, 'height' => $height];
        if (is_readable($watermarkPath) || is_readable($watermarkPath = $_SERVER['DOCUMENT_ROOT'] . '/' . $watermarkPath)) {
            $bNeedCreatePicture = false;
            $arSourceSize = [];
            $originalImageSizes = CFile::GetImageSize($_SERVER["DOCUMENT_ROOT"] . CFile::GetPath($imageId));
            CFile::ScaleImage($originalImageSizes["0"], $originalImageSizes["1"], ["width" => $width, "height" => $height], $resizeType, $bNeedCreatePicture, $arSourceSize, $arDestinationSize);
            $newWmWidth = $arDestinationSize['width'] * $wmScale;
            if ($wmMaxWidth && $newWmWidth > $wmMaxWidth) {
                $newWmWidth = $wmMaxWidth;
            }
            $watermarkResized = $_SERVER['DOCUMENT_ROOT'] . "/upload/watermark/" . SITE_TEMPLATE_ID . $newWmWidth . ".png";
            if (!is_readable($watermarkResized)) {
                CFile::ResizeImageFile($watermarkPath, $watermarkResized, ["width" => $newWmWidth, 'height' => $arDestinationSize['height']],
                    BX_RESIZE_IMAGE_PROPORTIONAL, false, 95, []);
            }
            if (is_readable($watermarkResized)) {
                $arFilters[] = [
                    "name" => "watermark",
                    "size" => "real",
                    "position" => $wmPosition,
                    "file" => $watermarkResized
                ];
            }
        }

        return CFile::ResizeImageGet($imageId, $arDestinationSize, $resizeType, true, $arFilters, false, 100);
    }
```
