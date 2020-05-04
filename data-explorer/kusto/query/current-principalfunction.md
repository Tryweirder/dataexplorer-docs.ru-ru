---
title: current_principal () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается current_principal () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 0561ac200105015e6d1c1cce1c16fe5f60fc2ccf
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737714"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Возвращает текущее имя участника, выполняющего запрос.

**Синтаксис**

`current_principal()`

**Возвращает**

Текущий основной субъект с полным именем (FQN) в виде `string`.  
Строка формируется следующим образом:  
*ПринЦиплатипе*`=`*PrincipalId*PrincipalId`;`*TenantId*

**Пример**

```kusto
print fqn=current_principal()
```

|Fqn|
|---|
|аадусер = 346e950e-4a62-42bf-96f5-4cf4eac3f11e; 72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
