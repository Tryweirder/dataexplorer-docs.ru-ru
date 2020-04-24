---
title: обратный () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описано обратное () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e95246e0586dff7dd89dc2658c7fae08b1bbaddf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510308"
---
# <a name="reverse"></a>reverse()

Функция делает обратную строку ввода.

Если значение ввода не имеет типа строки, функция принудительно отбрасывает значение строки.

**Синтаксис**

`reverse(`*Источник*`)`

**Аргументы**

* *источник*: значение ввода.  

**Возвращает**

Обратный порядок значения строки.

**Примеры**

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|Rstr|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ЗИХВУТСР-ПоНЛКЛИХГГФЕДКБА|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|rtimespan|
|---|---|---|---|
|54321|54.321|0000000.00:21T51-01-7102|00:00:30|




 