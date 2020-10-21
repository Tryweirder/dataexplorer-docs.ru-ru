---
title: Оператор DataTable — обозреватель данных Azure
description: В этой статье описывается оператор DataTable в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6a5f12c947d62b7ec13ab6d9d6a4564881de08a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247844"
---
# <a name="datatable-operator"></a>Оператор DataTable

Возвращает таблицу, схема и значения которой определены в самом запросе.

> [!NOTE]
> У этого оператора нет входных данных конвейера.

## <a name="syntax"></a>Синтаксис

`datatable``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *скаларвалуе* [ `,` *скаларвалуе* ...]`]`

## <a name="arguments"></a>Аргументы

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: эти аргументы определяют схему таблицы. Аргументы используют тот же синтаксис, который использовался при определении таблицы.
  Дополнительные сведения см. в статье [Создание таблицы](../management/create-table-command.md).
* *Скаларвалуе*: константное скалярное значение для вставки в таблицу. Количество значений должно быть целым числом, кратным столбцам в таблице. *N*-ое значение должно иметь тип, соответствующий столбцу *n*  %  *нумколумнс*.

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: эти аргументы определяют схему таблицы.
* *Скаларвалуе*: константное скалярное значение для вставки в таблицу. Количество значений должно быть целым числом, кратным столбцам в таблице. *N*-ое значение должно иметь тип, соответствующий столбцу *n*  %  *нумколумнс*.

::: zone-end

## <a name="returns"></a>Результаты

Этот оператор возвращает таблицу данных указанной схемы и данных.

## <a name="example"></a>Пример

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
