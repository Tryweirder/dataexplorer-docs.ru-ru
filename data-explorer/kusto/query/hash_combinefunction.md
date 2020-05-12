---
title: hash_combine () — обозреватель данных Azure
description: В этой статье описывается hash_combine () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 1925d9b27382dd3a888e14243bfecad51d37db0d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226709"
---
# <a name="hash_combine"></a>hash_combine()

Объединяет хэш-значения двух или более хэшей.

**Синтаксис**

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

**Аргументы**

* *H1*: длинное значение, представляющее первое значение хэша.
* *H2*: длинное значение, представляющее второе хэш-значение.
* *HN*: длинное значение, представляющее n-значное значение.

**Возвращает**

Объединенное хэш-значение заданных скаляров.

**Примеры**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|H1|H2|вышают|
|---|---|---|---|---|
|Привет|World|753694413698530628|1846988464401551951|— 1440138333540407281|
