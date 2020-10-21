---
title: current_principal () — обозреватель данных Azure
description: В этой статье описывается current_principal () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a2530b93bd5102b7c21aa535eb8e7ca7c4360ddf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252497"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Возвращает имя текущего участника, выполняющего запрос.

## <a name="syntax"></a>Синтаксис

`current_principal()`

## <a name="returns"></a>Результаты

Текущее полное имя участника (FQN) в виде `string` .  
Формат строки:  
*ПринЦиплатипе* `=` *PrincipalId* `;` *ИД* клиента

## <a name="example"></a>Пример

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
