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
ms.openlocfilehash: 3d0f389264d078d2b55ac06214bb3b820fcf7f13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347601"
---
# <a name="hash_many"></a>hash_many()

Возвращает объединенное хэш-значение нескольких значений.

## <a name="syntax"></a>Синтаксис

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

## <a name="arguments"></a>Аргументы

* *S1*, *S2*,..., *SN*: входные значения, которые будут хэшированы вместе.

## <a name="returns"></a>Возвращаемое значение

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
