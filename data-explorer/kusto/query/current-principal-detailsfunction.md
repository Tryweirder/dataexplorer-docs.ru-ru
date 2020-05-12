---
title: current_principal_details () — обозреватель данных Azure
description: В этой статье описывается current_principal_details () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: f71770d2cc9d44987731a247fa8eb945ed323391
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227522"
---
# <a name="current_principal_details"></a>current_principal_details()

Возвращает сведения об участнике, выполняющем запрос.

**Синтаксис**

`current_principal_details()`

**Возвращает**

Сведения о текущем участнике в виде `dynamic` .

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName": " user@fabrikam.com ",<br>  "IdentityProvider": " https://sts.windows.net ",<br>  "Authority": "72f988bf-86f1-41af-91ab-2d7cd011db47",<br>  "MFA": "true",<br>  "Тип": "Аадусер",<br>  "DisplayName": "Джеймс Иванов (UPN: user@fabrikam.com )",<br>  ObjectId: "346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "FQN": NULL,<br>  "Примечания": NULL<br>}|
