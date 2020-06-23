---
title: parse_json () — обозреватель данных Azure
description: В этой статье описывается parse_json () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac6bf9a8dbd54c3afca1c00f487e6ba564e65ce9
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264986"
---
# <a name="parse_json"></a>parse_json()

Интерпретирует `string` как значение JSON и возвращает значение в виде `dynamic` .

Эта функция лучше, чем [функция екстрактжсон ()](./extractjsonfunction.md) , если необходимо извлечь более одного элемента составного объекта JSON.

**Синтаксис**

`parse_json(`*json*`)`

Псевдонимы:
- [todynamic()](./todynamicfunction.md)
- [тубжект ()](./todynamicfunction.md)

**Аргументы**

* *JSON*: выражение типа `string` . Он представляет [значение в формате JSON](https://json.org/)или выражение типа [dynamic](./scalar-data-types/dynamic.md), представляющее фактическое `dynamic` значение.

**Возвращает**

Объект типа `dynamic` , определяемый значением *JSON*:
* Если *JSON* имеет тип `dynamic` , его значение используется как есть.
* Если *JSON* имеет тип `string` и является [правильно отформатированной строкой JSON](https://json.org/), то строка анализируется, и возвращается полученное значение.
* Если *JSON* имеет тип `string` , но не является [правильно форматированной строкой JSON](https://json.org/), возвращаемое значение является объектом типа `dynamic` , который содержит исходное `string` значение.

**Пример**

В следующем примере `context_custom_metrics` представляет собой `string`, который выглядит следующим образом.

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

затем следующий фрагмент CSL извлекает значение `duration` слота в объекте и получает два слота `duration.value` и `duration.min` ( `118.0` и `110.0` соответственно).

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Примечания**

Обычно есть строка JSON, описывающая контейнер свойств, в котором один из "слотов" является другой строкой JSON. 

Пример:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

В таких случаях необходимо вызывать не только `parse_json` дважды, но и убедиться, что во втором вызове `tostring` используется. В противном случае второй вызов метода `parse_json` просто передает входные данные на выход "как есть", так как его объявленный тип — `dynamic` .

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
