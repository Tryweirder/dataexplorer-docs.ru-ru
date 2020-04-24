---
title: toint() - Azure Data Explorer Документы Майкрософт
description: Эта статья описывает toint() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570e13dc816c8a7e6d5baa488912fd8def5d2883
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506109"
---
# <a name="toint"></a>toint()

Преобразует входные данные в целый ряд (подписанный 32-разрядный) номерпредставления.

```kusto
toint("123") == 123
```

**Синтаксис**

`toint(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в ряды. 

**Возвращает**

Если конверсия будет успешной, результат будет целым числом.
Если конверсия не будет `null`успешной, результат будет .
 
*Примечание*: Предпочитаюиспользовать использование [int()](./scalar-data-types/int.md) когда это возможно.