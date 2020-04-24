---
title: binary_all_or () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана binary_all_or() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 5de240f606e53b26996ebfe11073170758233e2c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517720"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or() (функция агрегирования)

Накапливает значения с `OR` помощью бинарной операции на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `binary_all_or(` *Expr*`)`

**Аргументы**

* *Expr*: длинный номер.

**Возвращает**

Возвращает значение, агрегируемое `OR` с помощью двоичной операции по записям на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).

**Пример**

Производство «кафе-продуктов» с `OR` использованием бинарных операций:

```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|набор по|
|---|
|CAFEF00D|