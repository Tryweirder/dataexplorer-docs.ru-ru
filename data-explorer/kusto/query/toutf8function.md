---
title: to_utf8 () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается to_utf8 () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d48ed99e517e0b1e5d498e80deaa48dc1cd3601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505837"
---
# <a name="to_utf8"></a>to_utf8()

Возвращает динамический массив символов unicode входиной строки (обратная операция make_string).

**Синтаксис**

`to_utf8(`*Источник*`)`

**Аргументы**

* *источник*: Строка источника для преобразования.

**Возвращает**

Возвращает динамический массив символов единокода, вкоторыехав строку, предоставленную этой функции.
См.) [`make_string()`](makestringfunction.md)

**Примеры**

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|Arr|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|Arr|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
