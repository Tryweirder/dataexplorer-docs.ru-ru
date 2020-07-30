---
title: Sqrt () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Sqrt () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 76f5c8c5f8c1a0b9f685ae88df1ab624dc446150
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350967"
---
# <a name="sqrt"></a>sqrt()

Возвращает квадратный корень функции.  

## <a name="syntax"></a>Синтаксис

`sqrt(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число >= 0.

## <a name="returns"></a>Результаты

* Положительное число, такое как `sqrt(x) * sqrt(x) == x`
* `null`, если аргумент является отрицательным числом или не может быть преобразован в значение `real`. 