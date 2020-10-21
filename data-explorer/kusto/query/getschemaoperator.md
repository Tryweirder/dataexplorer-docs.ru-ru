---
title: Оператор GetSchema — Azure обозреватель данных
description: В этой статье описан оператор GetSchema в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6960a737b0a71a6b921a6a58750e48f5c3fb9da0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247377"
---
# <a name="getschema-operator"></a>Оператор getschema 

Создание таблицы, представляющей табличную схему входных данных.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

## <a name="syntax"></a>Синтаксис

*T* `| ``getschema`

## <a name="example"></a>Пример

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
