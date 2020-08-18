---
title: series_ifft () — обозреватель данных Azure
description: В этой статье описывается функция series_ifft () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: c5862e9c18959726f27ea7d4237058f36a408b5c
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502168"
---
# <a name="series_ifft"></a>series_ifft ()

Применяет обратное быстрое преобразование Фурье (ИФФТ) к ряду.  

Функция series_ifft () принимает ряд комплексных чисел в домене Frequency и преобразует их обратно в время или пространственный домен, используя [Быстрое преобразование Фурье](https://en.wikipedia.org/wiki/Fast_Fourier_transform). Эта функция является дополняющей функцией [series_fft](series-fft-function.md). Обычно исходные ряды преобразуются в домен Frequency для обработки Спектрал, а затем возвращаются к времени или пространственному домену.

## <a name="syntax"></a>Синтаксис

`series_ifft(`*fft_real* [ `,` *fft_imaginary*]`)`

## <a name="arguments"></a>Аргументы

* *fft_real*: динамический массив числовых значений, представляющий реальный компонент преобразуемого ряда.
* *fft_imaginary*: аналогичный динамический массив, представляющий мнимую часть ряда. Этот параметр является необязательным и должен быть указан только в том случае, если входной ряд содержит комплексные числа.

## <a name="returns"></a>Возвращаемое значение

Функция возвращает сложный обратный ФФТ в двух рядах. Первый ряд для реального компонента и второй для мнимого компонента.

## <a name="example"></a>Пример

См. [series_fft](series-fft-function.md#example)
