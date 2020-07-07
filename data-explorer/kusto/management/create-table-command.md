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
ms.openlocfilehash: b071c4af6bc25650d18b1b66130941f73af551ff
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967100"
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
> Для создания нескольких таблиц используйте команду [. Create Tables](create-tables-command.md) , чтобы повысить производительность и снизить нагрузку на кластер.
