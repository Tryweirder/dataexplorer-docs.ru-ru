---
title: . ALTER DATABASE преттинаме — Azure обозреватель данных
description: В этой статье описывается `.alter` команда для довольно имени базы данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fc445a7d85f52d672b92163cc358d8f3a741c68
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294514"
---
# <a name="alter-database-prettyname"></a>. ALTER DATABASE преттинаме

Изменяет достаточное (понятное) имя базы данных.

Требуется [разрешение датабасеадмин](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.alter``database` *DatabaseName* `prettyname` DatabaseName `'` *Датабасепреттинаме*`'`

**Возврат выходных данных**
 
|Выходной параметр |Type |Описание 
|---|---|---
|имя_базы_данных |Строка |Имя базы данных
|преттинаме |Строка |Имя базы данных
