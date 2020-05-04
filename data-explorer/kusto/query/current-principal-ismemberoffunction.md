---
title: current_principal_is_member_of () — Azure обозреватель данных | Документация Майкрософт
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
ms.openlocfilehash: 4b6f7d0b9ab4074f16ca00b4a3febb1a17351736
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737765"
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

* *список выражений* — разделенный запятыми список строковых литералов, где каждый литерал является основной строкой полного имени (FQN), сформированной следующим образом:  
*ПринЦиплатипе*`=`*PrincipalId*PrincipalId`;`*TenantId*

| PrincipalType   | Префикс FQN  |
|-----------------|-------------|
| Пользователь AAD        | `aaduser=`  |
| Группа AAD       | `aadgroup=` |
| Приложение AAD | `aadapp=`   |

**Возвращает**

Функция возвращает:
* `true`: если текущий участник, выполняющий запрос, был успешно сопоставлен по крайней мере с одним входным аргументом.
* `false`: если текущий участник, выполняющий запрос, не был членом `aadgroup=` каких- `aaduser=` либо аргументов FQN и не равен ни одному из `aadapp=` аргументов FQN или.
* `(null)`: если текущий участник, выполняющий запрос, не был членом `aadgroup=` каких- `aaduser=` либо аргументов FQN и не равен ни одному из `aadapp=` аргументов FQN или, и хотя бы один аргумент FQN не был успешно разрешен (не пресед в AAD). 

> [!NOTE]
> Поскольку функция возвращает значение более чем в три состояния (`true`, `false`и `null`), важно полагаться только на положительные возвращаемые значения для подтверждения успешного членства. Иными словами, следующие выражения не совпадают:
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**Пример**

```kusto
print result=current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

| набор по |
|--------|
| (null) |

Использование динамического массива вместо мултпле аргументов:

```kusto
print result=current_principal_is_member_of(
    dynamic([
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    ]))
```

| набор по |
|--------|
| (null) |

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
