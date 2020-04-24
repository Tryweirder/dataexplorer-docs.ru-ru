---
title: strrep () - Azure Data Explorer Документы Майкрософт
description: В этой статье описана strrep() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 39b398e8fadb400c25cfeb97487c2ecf0669ad83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506721"
---
# <a name="strrep"></a>strrep()

[Повторяетданую строку](./scalar-data-types/string.md) при условии количества раз.

* В случае, если первый или третий аргумент не имеет типа строки, он будет принудительно преобразован в строку.

**Синтаксис**

`strrep(`*значение,**множитель,**делимитер*`)`

**Аргументы**

* *значение:* выражение ввода
* *мультипликатор*: положительное значение ажероприенки (от 1 до 1024)
* *делимитер*: дополнительное выражение строки (по умолчанию: пустая строка)

**Возвращает**

Значение повторяется в течение определенного количества раз, совмещенный с *делимитетом.*

В случае, если *множитель* превышает максимально допустимое значение (1024), строка ввода будет повторяться 1024 раза.
 
**Пример**

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|