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
ms.openlocfilehash: cf10fcfd66b7a9240746a904e4979c8091c3518b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348689"
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
