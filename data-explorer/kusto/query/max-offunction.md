---
title: max_of () — обозреватель данных Azure
description: В этой статье описывается max_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2045436de09bc31fa0378824310fa872478b861
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346836"
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

|result|
|---|
|17|
