---
title: current_principal () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны current_principal () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 36cebef7db3042bb59ccc5c7c25a56b2c1a661dc
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766040"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Возвращает текущее основное имя, на ход которого работает запрос.

**Синтаксис**

`current_principal()`

**Возвращает**

Текущий основной полностью квалифицированных имя (ФЗН) в качестве `string`.  
Строка формируется как:  
*PrinciplaType*`=`*PrincipalId*`;`*TenantId*

**Пример**

```kusto
print fqn=current_principal()
```

|fqn|
|---|
|aaduser-346e950e-4a62-42bf-96f5-4cf4eac3f11e;72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
