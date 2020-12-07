---
title: . Создание таблицы — Azure обозреватель данных
description: В этой статье описывается создание таблицы в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 8cfdbe1420745620fcaaf6af81e4f750ca25c1cd
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321613"
---
# <a name="create-table"></a>.create table

Создает новую пустую таблицу.

Команда должна выполняться в контексте определенной базы данных.

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.create``table` *TableName* ([ColumnName: columnType],...)  [ `with` `(` [ `docstring` `=` *Документация*] [ `,` `folder` `=` *имя_папки*] `)` ]

Если таблица уже существует, команда вернет значение Success.

**Пример** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**Возврат выходных данных**

Возвращает схему таблицы в формате JSON, аналогичную:

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> Для создания нескольких таблиц используйте [`.create tables`](create-tables-command.md) команду для повышения производительности и снижения нагрузки на кластер.
