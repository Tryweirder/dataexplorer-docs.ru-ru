---
title: иснотемпти () — Azure обозреватель данных
description: В этой статье описывается иснотемпти () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a21031b07df3a4fa654fd13eb3761618308337b
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717348"
---
# <a name="isnotempty"></a>isnotempty()

Возвращает `true` , если аргумент не является пустой строкой и не равен null.

```kusto
isnotempty("") == false
```

**Синтаксис**

`isnotempty(`[*значение*]`)`

`notempty(`[*значение*] `)` --псевдоним`isnotempty`
