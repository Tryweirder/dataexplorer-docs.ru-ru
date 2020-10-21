---
title: current_principal_details () — обозреватель данных Azure
description: В этой статье описывается current_principal_details () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/08/2019
ms.openlocfilehash: 387504d49b2c8be52357be74e69cdbde581c1298
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252525"
---
# <a name="current_principal_details"></a>current_principal_details()

Возвращает сведения об участнике, выполняющем запрос.

## <a name="syntax"></a>Синтаксис

`current_principal_details()`

## <a name="returns"></a>Результаты

Сведения о текущем участнике в виде `dynamic` .

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print d=current_principal_details()
```

|d|
|---|
|{<br>  "UserPrincipalName": " user@fabrikam.com ",<br>  "IdentityProvider": " https://sts.windows.net ",<br>  "Authority": "72f988bf-86f1-41af-91ab-2d7cd011db47",<br>  "MFA": "true",<br>  "Тип": "Аадусер",<br>  "DisplayName": "Джеймс Иванов (UPN: user@fabrikam.com )",<br>  ObjectId: "346e950e-4a62-42bf-96f5-4cf4eac3f11e",<br>  "FQN": NULL,<br>  "Примечания": NULL<br>}|
