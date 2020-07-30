---
title: Reverse () — Azure обозреватель данных
description: В этой статье описывается обратная () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fa87dae85b158e162cea90c276057abb210ea23b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345782"
---
# <a name="reverse"></a>reverse()

Функция меняет порядок входной строки на обратный.
Если входное значение не относится `string` к типу, функция принудительно приводит значение к типу `string` .

## <a name="syntax"></a>Синтаксис

`reverse(`*источника*`)`

## <a name="arguments"></a>Аргументы

* *Source*: входное значение.  

## <a name="returns"></a>Результаты

Обратный порядок строкового значения.

## <a name="examples"></a>Примеры

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|RSTR|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|зиксввутсркпонмлкжихгфедкба|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|рдаубле|рдатетиме|ртимеспан|
|---|---|---|---|
|54321|54,321|Z 0000000.00:00:21T51-01-7102|00:00:30|
