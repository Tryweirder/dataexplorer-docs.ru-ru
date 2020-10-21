---
title: log () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается журнал () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 2b7c4f0ac50c35dba0a1d59540fdaafb0cda2bfd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241452"
---
# <a name="log"></a>log()

`log()` Возвращает натуральную функцию логарифма.  

## <a name="syntax"></a>Синтаксис

`log(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число > 0.

## <a name="returns"></a>Результаты

* Натуральный логарифм — это десятичный логарифм: обратная функция естественной экспоненциальной функции (exp).
* `null` значение, если аргумент имеет отрицательное значение или равен null или не может быть преобразован к `real` значению. 

## <a name="see-also"></a>См. также раздел

* Общие логарифмы (по основанию 10) см. в разделе [LOG10 ()](log10-function.md).
* Для логарифмов с основанием 2 см. раздел [log2 ()](log2-function.md) .