---
title: todate time() - Azure Data Explorer Документы Майкрософт
description: В этой статье описано time() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe3852195b1a79ab5c86176698099ed6e7ff7af
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506347"
---
# <a name="todatetime"></a>todatetime()

Преобразует входные в [datetime](./scalar-data-types/datetime.md) scalar.

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**Синтаксис**

`todatetime(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в [datetime.](./scalar-data-types/datetime.md) 

**Возвращает**

Если конверсия успешна, результат будет значением [времени даты.](./scalar-data-types/datetime.md)
Если конверсия не будет успешной, результат будет нулевым.
 
*Примечание*: Предпочитаюиспользовать время даты () по [возможности.](./scalar-data-types/datetime.md)