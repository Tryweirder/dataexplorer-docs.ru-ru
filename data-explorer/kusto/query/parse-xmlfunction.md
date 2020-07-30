---
title: parse_xml () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается parse_xml () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5e294a60545a081861597e772c39d2e7e99824e8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346377"
---
# <a name="parse_xml"></a>parse_xml()

Интерпретирует `string` как значение XML, преобразует значение в JSON и возвращает значение в виде `dynamic` .

## <a name="syntax"></a>Синтаксис

`parse_xml(`*xml*`)`

## <a name="arguments"></a>Аргументы

* *XML*: выражение типа `string` , представляющее значение в формате XML.

## <a name="returns"></a>Возвращаемое значение

Объект типа [dynamic](./scalar-data-types/dynamic.md) , который определяется значением *XML*или значением NULL, если формат XML является недопустимым.

Преобразование XML в JSON выполняется с помощью библиотеки [xml2json](https://github.com/Cheedoong/xml2json) .

Преобразование выполняется следующим образом.

XML                                |JSON                                            |Доступ
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | {"e": NULL}                                  | o. e
`<e>text</e>`                      | {"e": "Text"}                                | o. e
`<e name="value" />`               | {"e": {" @name ": "value"}}                     | o. e [" @name "]
`<e name="value">text</e>`         | {"e": {" @name ": "value", "#text": "Text"}} | o. e [" @name "] o. e ["#text"]
`<e> <a>text</a> <b>text</b> </e>` | {"e": {"a": "Text", "b": "Text"}}          | o. e. a o. e. b
`<e> <a>text</a> <a>text</a> </e>` | {"e": {"a": ["Text", "Text"]}}             | o. e. a [0] o. e. a [1]
`<e> text <a>text</a> </e>`        | {"e": {"#text": "Text", "a": "Text"}}      | 1 "o. e [" #text "] o. e. a

**Примечания**

* Максимальный размер входных данных `string` для `parse_xml` — 128 КБ. Более длинная интерпретация строк приведет к пустому объекту 
* Будут переведены только узлы элементов, атрибуты и текстовые узлы. Все остальное будет пропущено
 
## <a name="example"></a>Пример

В следующем примере `context_custom_metrics` представляет собой `string`, который выглядит следующим образом. 

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

затем следующий фрагмент CSL преобразует XML в следующий JSON:

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

и извлекает значение `duration` слота в объекте и получает два слота `duration.value` и `duration.min` ( `118.0` и `110.0` соответственно).

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```