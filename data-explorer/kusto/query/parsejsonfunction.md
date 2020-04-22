---
title: parse_json() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны parse_json () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87c2155864f039fb5b261786d0fa6eb6770af2f
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744682"
---
# <a name="parse_json"></a>parse_json()

Интерпретирует `string` значение JSON и возвращает `dynamic`значение как . 

Он превосходит [функцию extractjson()](./extractjsonfunction.md) при необходимости извлечения более одного элемента объекта соединения JSON.

**Синтаксис**

`parse_json(`*Json*`)`

Псевдонимы:
- [todynamic()](./todynamicfunction.md)
- [toobject()](./todynamicfunction.md)

**Аргументы**

* *json*: Выражение `string`типа, представляющее [значение jSON-формата,](https://json.org/)или выражение [динамического](./scalar-data-types/dynamic.md) `dynamic` типа, представляющее фактическое значение.

**Возвращает**

Объект типа, `dynamic` который определяется значением *json:*
* Если *json* имеет `dynamic`тип, то свое значение использовано как-будет.
* Если *json* имеет `string`тип, и [правильно-отформатированной строки JSON,](https://json.org/)строка разобрана и произведенное значение возвращается.
* Если *json* имеет `string`тип, но это **не** [правильно отформатированная строка JSON,](https://json.org/)то возвращенное значение является объектом типа, `dynamic` который удерживает исходное `string` значение.

**Пример**

В следующем примере `context_custom_metrics` представляет собой `string`, который выглядит следующим образом. 

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

затем следующий фрагмент CSL извлекает `duration` значение слота в объекте, а из `duration.value` этого `duration.min` `118.0` он `110.0`получает два слота, и (и, соответственно).

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Примечания**

Это несколько общие, чтобы иметь строку JSON, описывающие имущество мешок, в котором один из "слотов" является другой строки JSON. Пример:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

В таких случаях необходимо не только дважды вызвать `parse_json` вызов, но и убедиться, что во втором вызове `tostring` будет использовано. В противном случае, второй вызов `parse_json` будет просто передавать вход в выход как `dynamic`есть, потому что его заявленный тип:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```