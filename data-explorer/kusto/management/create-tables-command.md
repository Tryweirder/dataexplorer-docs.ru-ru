---
title: . Создание таблиц — Azure обозреватель данных
description: В этой статье описывается создание таблиц в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 28ee7e462245497dd14d3a14a1c0703cc71a8933
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321596"
---
# <a name="create-tables"></a>.create tables

Создает новые пустые таблицы в качестве групповой операции.

Команда должна выполняться в контексте определенной базы данных.

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.create``tables` *TableName1* ([ColumnName: columnType],...) [ `,` *TableName2* ([ColumnName: ColumnType],...)...] [ `with` `(` `folder` `=` *имя_папки*] `)` ]

Если таблица уже существует, команда вернет значение Success.
 
**Пример** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
