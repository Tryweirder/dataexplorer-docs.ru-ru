---
title: current_principal_is_member_of () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается current_principal_is_member_of () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6d33fd19181e1eba93b54f684864fed062701307
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663844"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

Проверяет членство в группе или основную идентификацию текущего основного запроса.

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

* *список выражений* - запятая разделенный список строк буквально, где каждый буквальный является основным полностью квалифицированным именем (ФЗН) строка формируется как:  
*PrinciplaType*`=`*PrincipalId*`;`*TenantId*

| PrincipalType   | Префикс ФЗН  |
|-----------------|-------------|
| Пользователь AAD        | `aaduser=`  |
| Группа AAD       | `aadgroup=` |
| Применение AAD | `aadapp=`   |

**Возвращает**

Функция возвращает:
* `true`: если текущий основной запуск запроса был успешно сопоставлен по крайней мере по одному аргументу ввода.
* `false`: если текущий основной запуск запроса `aadgroup=` не был участником каких-либо `aaduser=` аргументов ФЗН и не был равен ни одной из аргументов или `aadapp=` аргументов ФЗН.
* `(null)`: если текущий основной запуск запроса `aadgroup=` не был участником каких-либо `aaduser=` аргументов ФЗН и не был равен ни одному из аргументов или `aadapp=` аргументов ФЗН, и по крайней мере один аргумент ФЗН не был успешно решен (не был пресечен в AAD). 

> [!NOTE]
> Поскольку функция возвращает трехгосударственное`true` `false`значение `null`(, и), важно полагаться только на положительные значения возврата для подтверждения успешного членства. Другими словами, следующие выражения не являются одинаковыми:
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

Использование динамического массива вместо аргументов multple:

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

Это не поддерживается в Azure Monitor

::: zone-end