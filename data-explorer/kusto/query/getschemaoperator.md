---
title: Оператор getschema - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор getschema в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2faeee575f1af72cfad808253853ae96aba7a28f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514371"
---
# <a name="getschema-operator"></a>Оператор getschema 

Создать таблицу, представляющую таблетную схему ввода.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**Синтаксис**

*T* `| ``getschema`

**Пример**

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
|БайтсДоставлен|4|System.Int64|long