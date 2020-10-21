---
title: hash_many () — обозреватель данных Azure
description: В этой статье описывается hash_many () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a323491a2d3c4e78684c8bcaff6de8c55573d61a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243294"
---
# <a name="hash_many"></a>hash_many()

Возвращает объединенное хэш-значение нескольких значений.

## <a name="syntax"></a>Синтаксис

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

## <a name="arguments"></a>Аргументы

* *S1*, *S2*,..., *SN*: входные значения, которые будут хэшированы вместе.

## <a name="returns"></a>Результаты

Объединенное хэш-значение заданных скаляров.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|вышают|
|---|---|---|
|Привет|World|— 1440138333540407281|
