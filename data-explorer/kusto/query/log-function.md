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
ms.openlocfilehash: e81266bf43da93d2b36f0be5846e5d74f3157c7f
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103213"
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