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
ms.openlocfilehash: e04d71ad0e88d42f93f9f34576c4f5f6e752ddb6
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103250"
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