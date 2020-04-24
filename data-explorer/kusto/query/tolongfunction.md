---
title: tolong () - Исследователь данных Azure (ru) Документы Майкрософт
description: Эта статья описывает tolong() в Исследователе данных Лазурных Данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd354a39c048631ab98390c74cb7cd78ee5376b2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506092"
---
# <a name="tolong"></a>tolong()

Преобразует входные данные в длинное (подписанное 64-разрядное) представление чиней.

```kusto
tolong("123") == 123
```

**Синтаксис**

`tolong(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в длинное. 

**Возвращает**

Если конверсия будет успешной, результат будет длинным числом.
Если конверсия не будет `null`успешной, результат будет .
 
*Примечание*: Предпочитаюиспользовать использование [длинных ()](./scalar-data-types/long.md) когда это возможно.