---
title: вызов оператора с обозреватель данных Azure
description: В этой статье описывается оператор Invoke в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1114952cdafe04284e93815d11c160455416b87c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248978"
---
# <a name="invoke-operator"></a>Оператор invoke

Вызывает лямбда-выражение, получающее источник в `invoke` качестве аргумента табличного параметра.

```kusto
T | invoke foo(param1, param2)
```

> [!NOTE]
> Дополнительные сведения об объявлении лямбда-выражений, которые могут принимать табличные аргументы, см. в разделе [инструкции Let](./letstatement.md) .
 
## <a name="syntax"></a>Синтаксис

`T | invoke`*функция* `(` [*param1* `,` *Param2*]`)`

## <a name="arguments"></a>Аргументы

* *T*: табличный источник.
* *Function*: имя лямбда-выражения или имени функции для оценки.
* *param1*, *Param2* ...: дополнительные лямбда-аргументы.

## <a name="returns"></a>Результаты

Возвращает результат вычисленного выражения.

## <a name="example"></a>Пример

В следующем примере показано, как использовать `invoke` оператор для вызова лямбда-выражения:

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
