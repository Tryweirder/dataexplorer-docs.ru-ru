---
title: Sqrt () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Sqrt () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 81b31d8a692a2f68cdb8dd68b5c5f1e75057d3b0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242509"
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