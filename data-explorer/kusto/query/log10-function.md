---
title: log10 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается log10 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 978e40f3a2727b08dd404068ad364c7416361f66
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241413"
---
# <a name="log10"></a>log10()

`log10()` Возвращает стандартную функцию логарифма (Base-10).  

## <a name="syntax"></a>Синтаксис

`log10(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число > 0.

## <a name="returns"></a>Результаты

* Обычный логарифм является десятичным логарифмом: обратное значение экспоненциальной функции (exp) с основанием 10.
* `null` значение, если аргумент имеет отрицательное значение или равен null или не может быть преобразован к `real` значению. 

## <a name="see-also"></a>См. также раздел

* Для натуральных логарифмов (с основанием e) см. раздел [log ()](log-function.md).
* Для логарифмов с основанием 2 см. раздел [log2 ()](log2-function.md) .