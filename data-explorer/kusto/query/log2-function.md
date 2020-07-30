---
title: log2 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается log2 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: a404dfba70f3a624acc08e70ee4b24935d1d7135
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347108"
---
# <a name="log2"></a>log2()

`log2()`Возвращает функцию логарифма по основанию 2.  

## <a name="syntax"></a>Синтаксис

`log2(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число > 0.

## <a name="returns"></a>Результаты

* Логарифм является логарифмом по основанию 2: обратная функция экспоненциальной функции (exp) с основанием 2.
* `null`значение, если аргумент имеет отрицательное значение или равен null или не может быть преобразован к `real` значению. 

**См. также:**

* Для натуральных логарифмов (с основанием e) см. раздел [log ()](log-function.md).
* Общие логарифмы (по основанию 10) см. в разделе [LOG10 ()](log10-function.md).