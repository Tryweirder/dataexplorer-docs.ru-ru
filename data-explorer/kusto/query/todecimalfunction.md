---
title: todecimal () - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны тодецимальные () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d5ac70dfe71f80c3963292516e1b8516a297875
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506313"
---
# <a name="todecimal"></a>todecimal()

Преобразует входные данные в представительство десятичных чиней.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

**Синтаксис**

`todecimal(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в десятичную. 

**Возвращает**

Если преобразование успешно, результат будет десятичным числом.
Если конверсия не будет `null`успешной, результат будет .
 
*Примечание*: Предпочитаюиспользовать [реальные ()](./scalar-data-types/real.md) по возможности.