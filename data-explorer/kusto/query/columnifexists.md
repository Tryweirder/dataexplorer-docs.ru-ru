---
title: column_ifexists () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны column_ifexists () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4f7d4a2114aa2b9b3bb8ae3e951306a3ffb725e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517176"
---
# <a name="column_ifexists"></a>column_ifexists()

Принимает имя столбца в виде строки и значения по умолчанию. Возвращает ссылку на столбец, если она существует, в противном случае - возвращает значение по умолчанию.

**Синтаксис**

`column_ifexists(`*columnName*`, `*defaultValue*)

**Аргументы**

* *ColumnName*: Название столбца
* *значение значения по умолчанию:* значение для использования, если столбец не существует в контексте, в котором была использована функция.
                  Это значение может быть любым скалярным выражением (например, ссылка на другой столбец).

**Возвращает**

Если *столбецИмя* существует, то столбец, к которого он относится. В противном случае - *defaultValue*.

**Примеры**

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```