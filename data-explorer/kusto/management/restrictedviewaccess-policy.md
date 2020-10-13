---
title: Управление политиками Рестриктедвиевакцесс Kusto в Azure обозреватель данных
description: Сведения о командах Рестриктедвиевакцесс Policy в Azure обозреватель данных. См. раздел Просмотр, включение, отключение, изменение и удаление этой политики.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9ec328e3a15af3cedb833354f7e8ecea0fdc22c8
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002925"
---
# <a name="restricted_view_access-policy-command"></a>команда политики restricted_view_access

Политика *рестриктедвиевакцесс* описана [здесь](../management/restrictedviewaccesspolicy.md).

Ниже приведены команды управления для включения или отключения политики в таблицах базы данных.

Чтобы включить или отключить политику, выполните следующие действия.
```kusto
.alter table TableName policy restricted_view_access true|false
```

Включение и отключение политики для нескольких таблиц:
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

Чтобы просмотреть политику, выполните следующие действия.
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

Удаление политики (эквивалентно отключению):
```kusto
.delete table TableName policy restricted_view_access  
```
