---
title: hash_sha256 () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны hash_sha256 () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2147f4e9f2bd3d7df8f75ac704a4e4808e69bb3c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507605"
---
# <a name="hash_sha256"></a>hash_sha256()

Возвращает хэш-значение sha256 для вхотливного значения.

**Синтаксис**

`hash_sha256(`*Источник*`)`

**Аргументы**

* *источник*: Значение, необходимое для хэшена.

**Возвращает**

Значение хэша sha256 данного сcalar, кодируемое как гекс-строка (строка символов, каждый из которых представляет собой одно число Hex между 0 и 255).

> [!WARNING]
> Алгоритм, используемый этой функцией (SHA256), гарантированно не будет изменен в будущем, но очень сложен для расчета. Пользователям, которым нужна функция «легкого» хэша на протяжении всего одного запроса, рекомендуется вместо этого использовать [хэш-функции.)](./hashfunction.md)

**Примеры**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

В следующем примере используется функция hash_sha256 для выполнения запроса на столбце StartTime данных

```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```