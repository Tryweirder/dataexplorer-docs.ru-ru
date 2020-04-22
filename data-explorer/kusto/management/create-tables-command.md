---
title: таблицы .create - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны таблицы .create в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f76c4d4a2780b58d9596537aea183d026d086fc5
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744027"
---
# <a name="create-tables"></a>.create tables

Создает новые пустые таблицы в виде основной операции.

Команда должна работать в контексте определенной базы данных.

Требуется [разрешение пользователя базы данных.](../management/access-control/role-based-authorization.md)

**Синтаксис**

`.create``tables` *TableName1* («columnName:columnType»,,...) `,` *TableName2* («columnName:columnType»,, ...)...

Если какая-либо таблица уже существует, команда возвращает успех.
 
**Пример** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
