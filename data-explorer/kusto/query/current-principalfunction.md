---
title: current_principal () — обозреватель данных Azure
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
ms.openlocfilehash: b1d45fb8b0749a4be30854dd9b0120a7eb127bf2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227304"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Возвращает текущее имя участника, выполняющего запрос.

**Синтаксис**

`current_principal()`

**Возвращает**

Текущий основной субъект с полным именем (FQN) в виде `string` .  
Строка формируется следующим образом:  
*ПринЦиплатипе* `=` *PrincipalId* `;` *ИД* клиента

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
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
