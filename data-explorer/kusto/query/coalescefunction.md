---
title: объединение () — Azure обозреватель данных
description: В этой статье описывается объединение () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3eb5e533c2b4430f54909507e521912711c35811
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252652"
---
# <a name="coalesce"></a>coalesce()

Вычисляет список выражений и возвращает первое выражение, отличное от NULL (или не пустое для строки).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

## <a name="syntax"></a>Синтаксис

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

## <a name="arguments"></a>Аргументы

* *expr_i*— скалярное выражение для вычисления.
- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.


## <a name="returns"></a>Результаты

Значение первого *expr_i* , значение которого не равно null (или не является пустым для строковых выражений).

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|набор по|
|---|
|42|
