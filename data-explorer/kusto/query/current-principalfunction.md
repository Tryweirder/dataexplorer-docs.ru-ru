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
ms.openlocfilehash: 43e92edd74861acc8207a855243f9ec1e012070a
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717365"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Возвращает имя текущего участника, выполняющего запрос.

**Синтаксис**

`current_principal()`

**Возвращает**

Текущее полное имя участника (FQN) в виде `string` .  
Формат строки:  
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
