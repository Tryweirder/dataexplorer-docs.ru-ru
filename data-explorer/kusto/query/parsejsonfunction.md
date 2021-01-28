---
title: Функции todynamic() и parse_json() в Azure Data Explorer
description: В этой статье описываются функции todynamic() и parse_json() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/25/2021
ms.localizationpriority: high
ms.openlocfilehash: 680ce93ec2a3b39d14475689ac80793a02d86547
ms.sourcegitcommit: 8468c6886dc097032ef6db86992a5e8c9b18786e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98911950"
---
# <a name="todynamic-parse_json"></a>todynamic(), parse_json()

Интерпретирует `string` как значение JSON и возвращает значение типа `dynamic`. 

> [!NOTE]
> Функции `todynamic()` и `parse_json()` интерпретируются одинаково.

Если необходимо извлечь более одного элемента составного объекта JSON, эта функция более эффективная, чем [функция extractjson()](./extractjsonfunction.md). По возможности старайтесь использовать [dynamic()](./scalar-data-types/dynamic.md).

## <a name="syntax"></a>Синтаксис

`parse_json(`*json*`)`
`todynamic(`*json*`)`

<!-- deprecated aliases: `toobject()` and parsejson() -->

## <a name="arguments"></a>Аргументы

* *json*. Выражение типа `string`. Оно представляет [значение в формате JSON](https://json.org/) или выражение типа [dynamic](./scalar-data-types/dynamic.md), представляющее фактическое значение `dynamic`.

## <a name="returns"></a>Возвращаемое значение

Объект типа `dynamic`, который определяется значением *json*:
* Если *json* имеет тип `dynamic`, его значение используется без изменений.
* Если *json* имеет тип `string`и является [правильно отформатированной строкой JSON](https://json.org/), строка анализируется и возвращается значение.
* Если *json* имеет тип `string`, но является [неправильно отформатированной строкой JSON](https://json.org/), возвращаемое значение является объектом типа `dynamic`, который содержит исходное значение `string`.

## <a name="example"></a>Пример

В следующем примере `context_custom_metrics` представляет собой `string`, который выглядит следующим образом.

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Тогда следующий фрагмент CSL извлечет значение слота `duration` из объекта, а затем из него извлечет два слота `duration.value` и `duration.min` (`118.0` и `110.0` соответственно).

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Примечания**

Обычно используется строка JSON, описывающая контейнер свойств, в котором один из "слотов" является другой строкой JSON. 

Пример:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

В таких случаях нужно не только дважды вызывать `parse_json`, но и убедиться, что при втором вызове используется `tostring`. В противном случае при втором вызове `parse_json` входные данные будут переданы в вывод "как есть", поскольку его объявленным типом является `dynamic`.

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
