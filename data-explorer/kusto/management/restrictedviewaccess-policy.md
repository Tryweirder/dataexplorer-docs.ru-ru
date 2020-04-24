---
title: Политика ограниченного доступа в режим доступа к Интернету - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается политика RestrictedView Access в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9fcf37d30bfe3ab0f9c4b5d4a720e6a5ba4ffe34
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520457"
---
# <a name="restrictedviewaccess-policy"></a>Политика ограниченногодоступа

Политика *RestrictedViewAccess* описана [здесь](../management/restrictedviewaccesspolicy.md).

Команды управления для включения или отключения политики в таблице (ы) в базе данных являются следующими:

Для включения/отключать политику:
```kusto
.alter table TableName policy restricted_view_access true|false
```

Для включения/отключать политику нескольких таблиц:
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

Для просмотра политики:
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

Чтобы удалить политику (эквивалент отключения):
```kusto
.delete table TableName policy restricted_view_access  
```