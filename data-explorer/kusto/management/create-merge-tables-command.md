---
title: . Создание и объединение таблиц — Azure обозреватель данных
description: В этой статье описывается команда. CREATE-merge Tables в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f80ea54ece66440dc7a6b40d9d571f04bd3e26b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011522"
---
# <a name="create-merge-tables"></a>. CREATE — merge Tables

Позволяет создавать и расширять схемы существующих таблиц в одной массовой операции в контексте определенной базы данных.

> [!NOTE]
> Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).
> Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) для расширения существующих таблиц.

**Синтаксис**

`.create-merge``tables` *TableName1* ([имя_столбца: columnType],...) [ `,` *TableName2* ([ColumnName: ColumnType],...)...]

* Будут созданы указанные таблицы, которые не существуют.
* Для указанных таблиц, которые уже существуют, будут расширены их схемы.
    * Несуществующие столбцы будут добавлены в _конец_ схемы существующей таблицы.
    * Существующие столбцы, не указанные в команде, не будут удалены из схемы существующей таблицы.
    * Существующие столбцы, которые указаны с типом данных в команде, отличной от той, которая есть в схеме существующей таблицы, приведут к сбою. Ни одна таблица не будет создана или расширена.

**Пример**

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**Возврат выходных данных**

| TableName | имя_базы_данных  | Папка | DocString |
|-----------|---------------|--------|-----------|
| милогс    | топкомпарисон |        |           |
| мюсерс   | топкомпарисон |        |           |
