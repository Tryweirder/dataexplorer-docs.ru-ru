---
title: pack_array () — обозреватель данных Azure
description: В этой статье описывается pack_array () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3e9fbe7c11aeffbdc0274d4433eddd9a5463b262
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248682"
---
# <a name="pack_array"></a>pack_array()

Упаковывает все входные значения в динамический массив.

## <a name="syntax"></a>Синтаксис

`pack_array(`*Выражение1* `[` , ` *Expr2*]` ) '

## <a name="arguments"></a>Аргументы

* *Выражение1... N*: входные выражения для упаковки в динамический массив.

## <a name="returns"></a>Результаты

Динамический массив, содержащий значения Выражение1, выражение2,..., Експрн.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1, 2, 4]|
|[2, 4, 8]|
|[3, 6, 12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1, "2", "00:00:02"]|
|[2, "4", "00:00:04"]|
|[3, "6", "00:00:06"]|
