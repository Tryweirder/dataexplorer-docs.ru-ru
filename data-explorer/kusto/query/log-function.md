---
title: log () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается журнал () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 283cd1427dedb04b036d7cb23e650d8f0651a58c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347142"
---
# <a name="log"></a>log()

`log()`Возвращает натуральную функцию логарифма.  

## <a name="syntax"></a>Синтаксис

`log(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число > 0.

## <a name="returns"></a>Результаты

* Натуральный логарифм — это десятичный логарифм: обратная функция естественной экспоненциальной функции (exp).
* `null`значение, если аргумент имеет отрицательное значение или равен null или не может быть преобразован к `real` значению. 

**См. также:**

* Общие логарифмы (по основанию 10) см. в разделе [LOG10 ()](log10-function.md).
* Для логарифмов с основанием 2 см. раздел [log2 ()](log2-function.md) .