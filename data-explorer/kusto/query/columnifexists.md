---
title: column_ifexists () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается column_ifexists () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570a832673d94d57d69dfa9c8442d56802ecd7ac
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247863"
---
# <a name="column_ifexists"></a>column_ifexists()

Принимает имя столбца в виде строки и значение по умолчанию. Возвращает ссылку на столбец, если он существует; в противном случае — возвращает значение по умолчанию.

## <a name="syntax"></a>Синтаксис

`column_ifexists(`*ColumnName* `, ` *DefaultValue*)

## <a name="arguments"></a>Аргументы

* *ColumnName*: имя столбца.
* *DefaultValue*: значение, используемое, если столбец не существует в контексте, в котором была использована функция.
                  Это значение может быть любым скалярным выражением (например, ссылка на другой столбец).

## <a name="returns"></a>Результаты

Если *ColumnName* существует, то столбец, на который он ссылается. В противном случае — *DefaultValue*.

## <a name="examples"></a>Примеры

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```