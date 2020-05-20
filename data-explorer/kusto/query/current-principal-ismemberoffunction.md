---
title: current_principal_is_member_of () — обозреватель данных Azure
description: В этой статье описывается current_principal_is_member_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: fdd062abc02ff1d98ba935fc9016d0a1505e69c3
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550407"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

Проверяет членство в группе или идентификатор участника текущего участника, выполняющего запрос.

```kusto
print current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

**Синтаксис**

`current_principal_is_member_of`(`*list of string literals*`)

**Аргументы**

* *список выражений* — разделенный запятыми список строковых литералов, где каждый литерал является строкой полного имени участника (FQN), сформированной следующим образом:  
*ПринЦиплатипе* `=` *PrincipalId* `;` *ИД* клиента

| PrincipalType   | Префикс FQN  |
|-----------------|-------------|
| Пользователь AAD        | `aaduser=`  |
| Группа AAD       | `aadgroup=` |
| Приложение AAD | `aadapp=`   |

**Возвращает**
  
Функция возвращает:
* `true`: если текущий участник, выполняющий запрос, был успешно сопоставлен по крайней мере с одним входным аргументом.
* `false`: если текущий участник, выполняющий запрос, не был членом каких- `aadgroup=` либо аргументов FQN и не равен ни одному `aaduser=` из `aadapp=` аргументов FQN или.
* `(null)`: если текущий участник, выполняющий запрос, не был членом каких- `aadgroup=` либо аргументов FQN и не равен ни одному `aaduser=` из `aadapp=` аргументов FQN или, и по крайней мере один аргумент FQN не был успешно разрешен (не был НАЖАТ в Azure AD). 

> [!NOTE]
> Поскольку функция возвращает значение более чем в три состояния ( `true` , `false` и `null` ), важно полагаться только на положительные возвращаемые значения для подтверждения успешного членства. Иными словами, следующие выражения не совпадают:
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print result=current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

| result |
|--------|
| (null) |

Использование динамического массива вместо нескольких аргументов:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print result=current_principal_is_member_of(
    dynamic([
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    ]))
```

| result |
|--------|
| (null) |

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
