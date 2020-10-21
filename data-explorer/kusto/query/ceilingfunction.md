---
title: CEILING () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ceiling () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b307121e102229edbe62c4d4dd7f910ea2ce8756
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249334"
---
# <a name="ceiling"></a>ceiling()

Вычисляет наименьшее целое число, большее или равное указанному числовому выражению.

## <a name="syntax"></a>Синтаксис

`ceiling(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число.

## <a name="returns"></a>Результаты

* Наименьшее целое число, большее или равное указанному числовому выражению. 

## <a name="examples"></a>Примеры

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|