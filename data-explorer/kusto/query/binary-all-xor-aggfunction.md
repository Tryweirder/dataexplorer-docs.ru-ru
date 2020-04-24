---
title: binary_all_xor() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана binary_all_xor(функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a1908fe874576281c9ba45f23709845473b5725c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517703"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor() (функция агрегирования)

Накапливает значения с `XOR` помощью бинарной операции на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `binary_all_xor(` *Expr*`)`

**Аргументы**

* *Expr*: длинный номер.

**Возвращает**

Возвращает значение, агрегируемое `XOR` с помощью двоичной операции по записям на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).

**Пример**

Производство «кафе-продуктов» с `XOR` использованием бинарных операций:

```kusto
datatable(num:long)
[
  0x44404440,
  0x1E1E1E1E,
  0x90ABBA09,
  0x000B105A,
]
| summarize result = toupper(tohex(binary_all_xor(num)))
```

|набор по|
|---|
|CAFEF00D|