---
title: log10 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается log10 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 62813f5680e89c2bca0a6ec547fd7055ddc0e414
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103185"
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