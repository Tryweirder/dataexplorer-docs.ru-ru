---
title: таблицы .create-merge - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны таблицы .create-merge в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 408046e198710c4b825a399fcb90960411de1041
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744445"
---
# <a name="create-merge-tables"></a>таблицы .create-merge

Позволяет создавать и/или расширять схемы существующих таблиц в одной операции в контексте конкретной базы данных.

Требуется [разрешение пользователя базы данных,](../management/access-control/role-based-authorization.md)а также [разрешение администрирования интерфейса таблицы](../management/access-control/role-based-authorization.md) для расширения существующих таблиц.

**Синтаксис**

`.create-merge``tables` *TableName1* («columnName:columnType»,,...) `,` *TableName2* («columnName:columnType»,, ...)...

* Будут созданы определенные таблицы, которые не существуют.
* Указанные таблицы, которые уже существуют, будут расширены:
    * Несуществующие столбцы будут добавлены в _конце_ схемы существующей таблицы.
    * Существующие столбцы, не указанные в команде, не будут удалены из схемы существующей таблицы.
    * Существующие столбцы, указанные с другим типом данных в команде, к тому, что в схеме существующей таблицы приведет к сбою (таблицы не будут создаваться или расширяться).

**Пример** 

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**Выход возврата**

| TableName | имя_базы_данных  | Папка | DocString |
|-----------|---------------|--------|-----------|
| MyLogs    | ТопСравнени |        |           |
| MyUsers   | ТопСравнени |        |           |
