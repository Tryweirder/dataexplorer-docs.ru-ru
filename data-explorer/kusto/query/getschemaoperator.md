---
title: Оператор GetSchema — Azure обозреватель данных
description: В этой статье описан оператор GetSchema в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 333c4d59a7ed62fd031ab52019c10abd821fd858
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226760"
---
# <a name="getschema-operator"></a>Оператор getschema 

Создание таблицы, представляющей табличную схему входных данных.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**Синтаксис**

*T* `| ``getschema`

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|ColumnType|
|---|---|---|---|
|Отметка времени|0|System.DateTime|DATETIME|
|Язык|1|System.String|строка|
|Страница|2|System.String|строка|
|Представления|3|System.Int64|long
|битесделиверед|4|System.Int64|long
