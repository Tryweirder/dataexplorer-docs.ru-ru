---
title: оператор для ввода в систему - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор- вызвать в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41f19440795f4f302352a8dda5192c5c4790ea99
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513708"
---
# <a name="invoke-operator"></a>Оператор invoke

Вызывает lambda, который получает `invoke` источник в качестве табликового аргумента параметра.

```kusto
T | invoke foo(param1, param2)
```

**Синтаксис**

`T | invoke`*функция*`(`-*парам1* `,` *парам2*`)`

**Аргументы**

* *T*: Табулярный источник.
* *функция*: Имя выражения lambda или имени функции, которая будет оценена.
* *param1*, *param2* ... : дополнительные аргументы лямбда.

**Возвращает**

Возвращает результат оцененного выражения.

**Примечания**

См [пусть заявления](./letstatement.md) для получения более подробной информации о том, как объявить lambda выражения, которые могут принимать табличные аргументы.

**Пример**

В следующем примере `invoke` показано, как использовать оператора для вызова выражения лямбда:

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
