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
ms.openlocfilehash: 6d325861c7264c77535caf6df6c363ec801dd697
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347210"
---
# <a name="isnotempty"></a>isnotempty()

Возвращает `true` , если аргумент не является пустой строкой и не равен null.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Синтаксис

`isnotempty(`[*значение*]`)`

`notempty(`[*значение*] `)` --псевдоним`isnotempty`
