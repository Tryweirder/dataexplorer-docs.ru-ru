---
title: таблица .create - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается таблица .create в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 1c84b9b6cec5a5bb7ab620871f59c188bf71cef4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744232"
---
# <a name="create-table"></a>.create table

Создает новый пустой стол.

Команда должна работать в контексте определенной базы данных.

Требуется [разрешение пользователя базы данных.](../management/access-control/role-based-authorization.md)

**Синтаксис**

`.create``table` *ТаблицаИмя* («columnName:columnType»,,...)  `with` `(`Документация`docstring` `folder` *Documentation* `)`- `=` *FolderName*FolderName`,` `=`

Если таблица уже существует, команда вернет успех.

**Пример** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**Выход возврата**

Возвращает схему таблицы в формате JSON, так же, как:

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> Для создания нескольких таблиц используйте команду [таблиц ы .create](/create-tables.md) для повышения производительности и снижения нагрузки на кластер.

## <a name="create-merge-table"></a>таблица .create-merge

Создает новую таблицу или расширяет существующую таблицу. 

Команда должна работать в контексте определенной базы данных. 

Требуется [разрешение пользователя базы данных.](../management/access-control/role-based-authorization.md)

**Синтаксис**

`.create-merge``table` *ТаблицаИмя* («columnName:columnType»,,...)  `with` `(`Документация`docstring` `folder` *Documentation* `)`- `=` *FolderName*FolderName`,` `=`

Если таблица не существует, функции точно так же, как ".create таблицы" команда.

Если таблица T существует, и вы отправляете<columns specification>команду ".create-merge" T ()"

* Любой <columns specification> столбец, который ранее не существовал в T, будет добавлен к концу схемы T.
* Любой столбец в <columns specification> T, который не находится в не будет удален из T.
* Любой <columns specification> столбец в том, что существует в T, но с другим типом данных приведет к команде к сбою.
