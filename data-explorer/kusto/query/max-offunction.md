---
title: max_of () — обозреватель данных Azure
description: В этой статье описывается max_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2a9cbb64ce1bbbc7d58b66c260d7968d8a60748
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248953"
---
# <a name="max_of"></a>max_of()

Возвращает максимальное значение нескольких вычисленных числовых выражений.

```kusto
max_of(10, 1, -3, 17) == 17
```

## <a name="syntax"></a>Синтаксис

`max_of``(` *expr_1* `,` *expr_2* ...`)`

## <a name="arguments"></a>Аргументы

* *expr_i*— скалярное выражение для вычисления.

- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.

## <a name="returns"></a>Результаты

Максимальное значение всех выражений аргументов.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|набор по|
|---|
|17|
