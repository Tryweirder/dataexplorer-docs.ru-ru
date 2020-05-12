---
title: hash_many () — обозреватель данных Azure
description: В этой статье описывается hash_many () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 66ca1e5ff330a4b39ab769b0e3e8d6359eed9c00
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226675"
---
# <a name="hash_many"></a>hash_many()

Возвращает объединенное хэш-значение нескольких значений.

**Синтаксис**

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

**Аргументы**

* *S1*, *S2*,..., *SN*: входные значения, которые будут хэшированы вместе.

**Возвращает**

Объединенное хэш-значение заданных скаляров.

**Примеры**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|вышают|
|---|---|---|
|Привет|World|— 1440138333540407281|
