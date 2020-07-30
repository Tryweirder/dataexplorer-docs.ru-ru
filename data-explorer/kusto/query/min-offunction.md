---
title: min_of () — обозреватель данных Azure
description: В этой статье описывается min_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ed8d14a4e793f253342c1b52269678874c96660f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346768"
---
# <a name="min_of"></a>min_of()

Возвращает минимальное значение нескольких вычисленных числовых выражений.

```kusto
min_of(10, 1, -3, 17) == -3
```

## <a name="syntax"></a>Синтаксис

`min_of``(` *expr_1* `,` *expr_2* ...`)`

## <a name="arguments"></a>Аргументы

* *expr_i*— скалярное выражение для вычисления.

- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.

## <a name="returns"></a>Результаты

Минимальное значение всех выражений аргументов.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|–3|
