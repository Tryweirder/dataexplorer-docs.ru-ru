---
title: toguid() - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны тогиды () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb1df5fe91b75e5d3b7d1a9f40b8b3079cac9944
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506143"
---
# <a name="toguid"></a>toguid()

Преобразует входные [`guid`](./scalar-data-types/guid.md) данные в представление.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

**Синтаксис**

`toguid(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в [`guid`](./scalar-data-types/guid.md) scalar. 

**Возвращает**

Если преобразование успешно, [`guid`](./scalar-data-types/guid.md) результат будет scalar.
Если конверсия не будет `null`успешной, результат будет .

*Примечание*: Предпочитаюиспользовать использование [гида ()](./scalar-data-types/guid.md) по возможности.