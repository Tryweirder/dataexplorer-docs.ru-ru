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
ms.openlocfilehash: 9da59a53819396cf2cbd522f4a1e1296f585bf2f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617553"
---
# <a name="restrictedviewaccess-policy"></a>Политика Рестриктедвиевакцесс

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