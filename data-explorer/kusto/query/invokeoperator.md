---
title: вызов оператора с обозреватель данных Azure
description: В этой статье описывается оператор Invoke в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: debd0a83f4c27fa05415805404ad450bab1d3ca1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347380"
---
# <a name="invoke-operator"></a>Оператор invoke

Вызывает лямбда-выражение, получающее источник в `invoke` качестве аргумента табличного параметра.

```kusto
T | invoke foo(param1, param2)
```

## <a name="syntax"></a>Синтаксис

`T | invoke`*функция* `(` [*param1* `,` *Param2*]`)`

## <a name="arguments"></a>Аргументы

* *T*: табличный источник.
* *Function*: имя лямбда-выражения или имени функции для оценки.
* *param1*, *Param2* ...: дополнительные лямбда-аргументы.

## <a name="returns"></a>Возвращаемое значение

Возвращает результат вычисленного выражения.

**Примечания**

Дополнительные сведения об объявлении лямбда-выражений, которые могут принимать табличные аргументы, см. в разделе [инструкции Let](./letstatement.md) .

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
