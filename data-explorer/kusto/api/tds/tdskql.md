---
title: КЗЛ над TDS - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается КЗЛ над TDS в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2019
ms.openlocfilehash: 364db99141c528f4a822692124ebb870d7315bca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523313"
---
# <a name="kql-over-tds"></a>KQL через TDS

Kusto позволяет использовать конечную точку TDS для выполнения запросов, авторами которых является родной язык запросов [КЗЛ.](../../query/index.md) Эта функциональность предлагает более плавное мигрирование к Kusto. Например, можно создать задание SSIS для запроса Kusto с запросом КЗЛ.

## <a name="executing-kusto-stored-functions"></a>Выполнение сохраненных функций Kusto

Kusto позволяет выполнять [хранимые функции](../../query/schema-entities/stored-functions.md) так же, как вызов процедуры хранения S'L.

Например, сохраненная функция MyFunction:

|Имя |Параметры|Текст|Папка|DocString
|---|---|---|---|---
|MyFunction |(myLimit: длинный)| «StormEvents &#124; ограничить myLimit»|MyFolder|Функция демо с параметром||

можно назвать следующим образом:

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

Пожалуйста, обратите внимание, что сохраненные `kusto`функции должны быть вызваны с явной схемой под названием, чтобы различать сохраненные функции Kusto и эмулированные процедуры хранения системы S'L.

Функции, хранящиеся в Кусто, также можно вызывать из T-S'L, как и табулярные функции S'L:

```sql
SELECT * FROM kusto.MyFunction(10)
```

Рекомендуется создавать оптимизированные запросы к КЗЛ и инкапсулировать их в сохраненные функции, делая код запроса T-S'L минимальным.

## <a name="executing-kql-query"></a>Выполнение запроса КЗЛ

Как и в `sp_executesql`том, что касается `sp_execute_kql` сервера S'L, Kusto ввела процедуру хранения для выполнения запросов [КЗЛ,](../../query/index.md) включая параметризированные запросы.

Первый параметр `sp_execute_kql` — запрос КЗЛ. Дополнительные параметры могут быть введены, и они будут действовать как [параметры запроса.](../../query/queryparametersstatement.md)

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

Обратите внимание, что нет необходимости декларировать параметры при вызове через TDS, так как типы параметров устанавливаются через протокол.