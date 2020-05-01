---
title: ККЛ через TDS — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ККЛ over TDS в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 2c4443c0a9301dbc6bb3e65392163da0cc237f74
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617890"
---
# <a name="kql-over-tds"></a>KQL через TDS

Kusto позволяет конечным точкам TDS выполнять запросы, созданные на языке машинных запросов [ККЛ](../../query/index.md) . Эта возможность обеспечивает более плавную миграцию в сторону Kusto. Например, можно создать задания служб SSIS для запроса Kusto с помощью запроса ККЛ.

## <a name="executing-kusto-stored-functions"></a>Исполнение хранимых функций Kusto

Kusto позволяет выполнять [хранимые функции](../../query/schema-entities/stored-functions.md) , такие как вызов хранимых процедур SQL.

Например, хранимая функция MyFunction:

|Имя |Параметры|Текст|Папка|DocString
|---|---|---|---|---
|MyFunction |(Милимит: long)| {Стормевентс &#124; ограничение Милимит}|MyFolder|Демонстрационная функция с параметром||

может вызываться следующим образом:

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("kusto.MyFunction", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

> [! Примечание.] вызывайте хранимые функции с явной `kusto`схемой с именем, чтобы различать хранимые функции Kusto и эмулированные системные хранимые процедуры SQL.

Вы также можете вызывать хранимые функции Kusto из T-SQL, например табличные функции SQL:

```sql
SELECT * FROM kusto.MyFunction(10)
```

Создавайте оптимизированные запросы ККЛ и инкапсулируют их в хранимых функциях, делая код запроса T-SQL минимальным.

## <a name="executing-kql-query"></a>Выполнение запроса ККЛ

Хранимая процедура `sp_execute_kql` выполняет запросы [ККЛ](../../query/index.md) (включая параметризованные запросы). Эта процедура аналогична SQL Server `sp_executesql`.

Первым параметром `sp_execute_kql` является запрос ККЛ. Можно ввести дополнительные параметры, которые будут действовать как [Параметры запроса](../../query/queryparametersstatement.md).

Пример:

```csharp
  using (var connection = new SqlConnection(csb.ToString()))
  {
    await connection.OpenAsync();
    using (var command = new SqlCommand("sp_execute_kql", connection))
    {
      command.CommandType = CommandType.StoredProcedure;
      var query = new SqlParameter("@kql_query", SqlDbType.NVarChar);
      command.Parameters.Add(query);
      var parameter = new SqlParameter("mylimit", SqlDbType.Int);
      command.Parameters.Add(parameter);
      query.Value = "StormEvents | limit myLimit";
      parameter.Value = 3;
      using (var reader = await command.ExecuteReaderAsync())
      {
        // Read the response.
      }
    }
  }
```

> [! Примечание.] нет необходимости объявлять параметры при вызове через TDS, так как типы параметров устанавливаются через протокол.
