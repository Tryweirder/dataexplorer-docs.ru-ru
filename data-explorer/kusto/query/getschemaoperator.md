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
ms.openlocfilehash: 4fe19148ef8f410f04dc68f435734a2c2c425cca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347686"
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
|Timestamp|0|System.DateTime|DATETIME|
|Язык|1|System.String|строка|
|Страница|2|System.String|строка|
|Представления|3|System.Int64|long
|битесделиверед|4|System.Int64|long
