---
title: Т-СЗЛ - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается T-S'L в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d262d8b7587296c02a2a31d850919af0931bcde6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523415"
---
# <a name="t-sql"></a>T-SQL

Служба Kusto может интерпретировать и запускать запросы T-S'L с некоторыми языковыми ограничениями.
В то время как [язык запроса Kusto](../../query/index.md) является предпочтительным языком для Kusto, такая поддержка полезна для существующего инструмента, который не может быть легко преобразован в использование предпочтительного языка запроса, а также для случайного использования Kusto людьми, знакомыми с S'L.

> [!NOTE]
> Kusto не поддерживает команду DDL таким образом, поддерживаются `SELECT` только заявления T-S'SL. Подробнее об основных различиях между серверами и Кюсто в отношении T-S'L читайте в материале [«S'L» (sL, известные проблемы.](./sqlknownissues.md)

## <a name="querying-kusto-from-kustoexplorer-with-t-sql"></a>Запрос Кусто от Kusto.Explorer с T-S'L

Инструмент Kusto.Explorer поддерживает отправку запросов T-S'L в Kusto.
Чтобы поручить Kusto.Explorer выполнить запрос в этом режиме, подготовите запрос к пустой линии комментариев T-S'L. Пример:

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>От языка запроса T-S'L до Kusto

Kusto поддерживает перевод запросов T-S'L на язык запроса Kusto. Это может быть использовано, например, людьми, знакомыми с S'L, которые хотят лучше понимать язык запроса Kusto. Чтобы вернуть эквивалентный язык запроса Kusto в `SELECT` некоторые заявления `EXPLAIN` T-S'L, просто добавьте перед запросом.

Например, следующий запрос T-S'L:

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

Производит этот выход:

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```