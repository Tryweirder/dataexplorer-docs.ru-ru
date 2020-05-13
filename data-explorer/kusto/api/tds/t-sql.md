---
title: Обозреватель данных T-SQL — Azure
description: В этой статье описывается T-SQL в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1115414358a1025d4931484b81d6eda76109e6cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373526"
---
# <a name="t-sql-support"></a>Поддержка T-SQL

[Язык запросов Kusto (ККЛ)](../../query/index.md) является предпочтительным языком запросов.
Однако поддержка T-SQL полезна для средств, которые невозможно легко преобразовать для использования ККЛ.  
Поддержка T-SQL также полезна для случайного использования пользователями, знакомыми с SQL.

Kusto может интерпретировать и выполнять запросы T-SQL с некоторыми ограничениями языка.

> [!NOTE]
> Kusto не поддерживает команды DDL. Поддерживаются только инструкции T-SQL `select` . Дополнительные сведения о главных различиях в T-SQL см. в статье [Известные проблемы SQL](./sqlknownissues.md).

## <a name="querying-from-kustoexplorer-with-t-sql"></a>Запросы из Kusto. Explorer с помощью T-SQL

Средство Kusto. Explorer поддерживает запросы T-SQL к Kusto.
Чтобы указать Kusto. Explorer выполнить запрос, начните запрос с пустой строкой комментария T-SQL ( `--` ). Пример:

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>От T-SQL до Kusto языка запросов

Kusto поддерживает преобразование запросов T-SQL в язык запросов Kusto (ККЛ). Этот перевод помогает людям, знакомым с SQL, лучше понять ККЛ.
Чтобы вернуть эквивалентную ККЛ из некоторой инструкции T-SQL `select` , добавьте `explain` перед запросом.

Например, следующий запрос T-SQL:

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

создает следующие выходные данные:

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```
