---
title: Оператор Count — Azure обозреватель данных
description: В этой статье описывается оператор Count в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 9a34734ebfee94646b2b2f15730f14f9d2709c6d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227520"
---
# <a name="count-operator"></a>Оператор count

Возвращает количество записей во входном наборе записей.

**Синтаксис**

`T | count`

**Аргументы**

*T*— табличные данные, записи которых будут подсчитаны.

**Возвращает**

Эта функция возвращает таблицу с одной записью и столбцом типа `long`. Значение единственной ячейки — количество записей в *T*. 

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
