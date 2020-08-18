---
title: series_fft () — обозреватель данных Azure
description: В этой статье описывается функция series_fft () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 32b3b978f57f1a346ccd697fa2d95d962b558a15
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502173"
---
# <a name="series_fft"></a>series_fft ()

Применяет быстрое преобразование Фурье (ФФТ) к ряду.  

Функция series_fft () принимает ряд комплексных чисел во время или пространственный домен и преобразует его в домен Frequency, используя [Быстрое преобразование Фурье](https://en.wikipedia.org/wiki/Fast_Fourier_transform). Преобразованный комплексный ряд представляет величину и фазу частот, которые появлялись в исходном ряду. Используйте дополняющую функцию [series_ifft](series-ifft-function.md) для преобразования из домена периодичности обратно в домен времени или пространственного домена.

## <a name="syntax"></a>Синтаксис

`series_fft(`*x_real* [ `,` *x_imaginary*]`)`

## <a name="arguments"></a>Аргументы

* *x_real*: динамический массив числовых значений, представляющий реальный компонент преобразуемого ряда.
* *x_imaginary*: аналогичный динамический массив, представляющий мнимую часть ряда. Этот параметр является необязательным и должен быть указан только в том случае, если входной ряд содержит комплексные числа.

## <a name="returns"></a>Возвращаемое значение

Функция возвращает сложный обратный ФФТ в двух рядах. Первый ряд для реального компонента и второй для мнимого компонента.

## <a name="example"></a>Пример

* Создание комплексных рядов, в которых действительные и мнимые компоненты являются чисто синусными волнами с различной частотой. Используйте ФФТ, чтобы преобразовать его в домен Frequency:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | render linechart with(ysplit=panels)
    ```
    
    Этот запрос возвращает *fft_y_real* и *fft_y_imag*:  
    
    :::image type="content" source="images/series-fft-function/series-fft.png" alt-text="ФФТ ряда" border="false":::
    
* Преобразуйте ряд в домен Frequency, а затем примените обратное преобразование, чтобы вернуться к исходным рядам.

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let sinewave=(x:double, period:double, gain:double=1.0, phase:double=0.0)
    {
        gain*sin(2*pi()/period*(x+phase))
    }
    ;
    let n=128;      //  signal length
    range x from 0 to n-1 step 1 | extend yr=sinewave(x, 8), yi=sinewave(x, 32)
    | summarize x=make_list(x), y_real=make_list(yr), y_imag=make_list(yi)
    | extend (fft_y_real, fft_y_imag) = series_fft(y_real, y_imag)
    | extend (y_real2, y_image2) = series_ifft(fft_y_real, fft_y_imag)
    | project-away fft_y_real, fft_y_imag   //  too many series for linechart with panels
    | render linechart with(ysplit=panels)
    ```
    
    Этот запрос возвращает *y_real2* и * y_imag2, которые совпадают с *y_real* и *y_imag*:  
    
    :::image type="content" source="images/series-fft-function/series-ifft.png" alt-text="Иффт ряда" border="false":::
    