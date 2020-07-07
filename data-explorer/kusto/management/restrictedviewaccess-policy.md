---
title: Управление политиками Рестриктедвиевакцесс Kusto в Azure обозреватель данных
description: В этой статье описывается политика Рестриктедвиевакцесс в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 33f21bdad11555ad2a55f285cbf40239236c561f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967610"
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