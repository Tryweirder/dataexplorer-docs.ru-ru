---
title: . Создание таблиц — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается создание таблиц в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: ff8b3cfae6d3364b4d094f588c8130761fa5cb31
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966998"
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
