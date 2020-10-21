---
title: log2 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается log2 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: c5bab8e2f4106ddafc085fa35da424db60616a46
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243238"
---
# <a name="log2"></a>log2()

`log2()` Возвращает функцию логарифма по основанию 2.  

## <a name="syntax"></a>Синтаксис

`log2(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число > 0.

## <a name="returns"></a>Результаты

* Логарифм является логарифмом по основанию 2: обратная функция экспоненциальной функции (exp) с основанием 2.
* `null` значение, если аргумент имеет отрицательное значение или равен null или не может быть преобразован к `real` значению. 

## <a name="see-also"></a>См. также раздел

* Для натуральных логарифмов (с основанием e) см. раздел [log ()](log-function.md).
* Общие логарифмы (по основанию 10) см. в разделе [LOG10 ()](log10-function.md).