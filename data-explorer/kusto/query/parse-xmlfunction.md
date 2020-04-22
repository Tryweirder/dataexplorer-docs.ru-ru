---
title: parse_xml () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается parse_xml () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ace60d0f9652d75b0f55cce4b1b622af20b42dc1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663698"
---
# <a name="parse_xml"></a>parse_xml()

Интерпретирует `string` значение XML, преобразует значение в Значение JSON `dynamic`и возвращает значение как .

**Синтаксис**

`parse_xml(`*Xml*`)`

**Аргументы**

* *xml*: Выражение `string`типа, представляющее значение XML-формата.

**Возвращает**

Объект [типдинамической,](./scalar-data-types/dynamic.md) которая определяется значением *xml*, или недействительным, если формат XML является недействительным.

Преобразование XML в JSON осуществляется с помощью библиотеки [xml2json.](https://github.com/Cheedoong/xml2json)

Преобразование выполняется следующим образом:

XML                                |JSON                                            |Доступ
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | "е": null                                  | o.e
`<e>text</e>`                      | "е": "текст"                                | o.e
`<e name="value" />`               | "е":@name": "значение"                     | o.e"@name""
`<e name="value">text</e>`         | "е": ":@name"значение", "#text": "текст" | o.e"@name"о.е"#text"
`<e> <a>text</a> <b>text</b> </e>` | "е": "а": "текст", "б": "текст"          | o.e.a o.e.b
`<e> <a>text</a> <a>text</a> </e>` | "е": "а": "текст", "текст"             | o.e.a.0" o.e.a
`<e> text <a>text</a> </e>`        | "е": "#text": "текст", "а": "текст"      | 1'o.e"#text" o.e.a

**Примечания**

* Максимальная длина `string` `parse_xml` ввода составляет 128 кБ. Более длинная интерпретация строк приведет к нулю объекта 
* Будут переведены только элементные узлы, атрибуты и текстовые узлы. Все остальное будет пропущено
 
**Пример**

В следующем примере `context_custom_metrics` представляет собой `string`, который выглядит следующим образом.
<!--check this code formatting-->

```xml
<?xml version="1.0" encoding="UTF-8"?>
<duration>
    <value>118.0</value>
    <count>5.0</count>
    <min>100.0</min>
    <max>150.0</max>
    <stdDev>0.0</stdDev>
    <sampledValue>118.0</sampledValue>
    <sum>118.0</sum>
</duration>
```

затем следующий фрагмент CSL переводит XML на следующий JSON:

```json
{
    "duration": {
        "value": 118.0,
        "count": 5.0,
        "min": 100.0,
        "max": 150.0,
        "stdDev": 0.0,
        "sampledValue": 118.0,
        "sum": 118.0
    }
}
```

и получает значение `duration` слота в объекте, и от этого он `duration.value` получает `duration.min` `118.0` два `110.0`слота, и (и , соответственно).

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```