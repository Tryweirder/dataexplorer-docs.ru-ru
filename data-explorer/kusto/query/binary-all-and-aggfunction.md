---
title: binary_all_and () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается binary_all_and (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 4dfe4a2881f100a4bea3e9d418022c75b2621087
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517754"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and() (функция агрегирования)

Накапливает значения с `AND` помощью бинарной операции на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `binary_all_and(` *Expr*`)`

**Аргументы**

* *Expr*: длинный номер.

**Возвращает**

Возвращает значение, агрегируемое `AND` с помощью двоичной операции по записям на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).

**Пример**

Производство «кафе-продуктов» с `AND` использованием бинарных операций:

```kusto
datatable(num:long)
[
  0xFFFFFFFF, 
  0xFFFFF00F,
  0xCFFFFFFD,
  0xFAFEFFFF,
]
| summarize result = toupper(tohex(binary_all_and(num)))
```

|набор по|
|---|
|CAFEF00D|