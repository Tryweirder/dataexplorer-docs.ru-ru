---
title: . Создание таблицы — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается создание таблицы в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 25554b5485562179d849e846fc5e71c587815e86
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108428"
---
# <a name="create-table"></a>.create table

Создает новую пустую таблицу.

Команда должна выполняться в контексте определенной базы данных.

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.create``table` *TableName* ([ColumnName: columnType],...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` Документация] [ `folder` имя_папки] `)`] `=`

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

## <a name="create-merge-table"></a>. CREATE — merge-таблица

Создание новой таблицы или расширение существующей. 

Команда должна выполняться в контексте определенной базы данных. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.create-merge``table` *TableName* ([ColumnName: columnType],...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` Документация] [ `folder` имя_папки] `)`] `=`

Если таблица не существует, функция работает точно так же, как и команда ". Create Table".

Если таблица T существует и вы отправляете команду «. CREATE-merge Table T (<columns specification>)», то:

* Любой столбец в <columns specification> , который ранее не существовал в t, будет добавлен в конец схемы t.
* Любой столбец в T, не <columns specification> являющийся, не будет удален из t.
* Любой столбец в <columns specification> , который существует в T, но с другим типом данных приведет к сбою команды.
