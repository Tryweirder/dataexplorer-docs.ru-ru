---
title: column_ifexists () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается column_ifexists () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5eeecf9e4756ac18cdeb5c6297aea1bcca5bac14
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348859"
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