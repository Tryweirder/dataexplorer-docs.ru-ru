---
title: иснотемпти () — Azure обозреватель данных
description: В этой статье описывается иснотемпти () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 42699b1d4a6f225213b2a2d55520cb019a983121
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253222"
---
# <a name="isnotempty"></a>isnotempty()

Возвращает `true` , если аргумент не является пустой строкой и не равен null.

```kusto
isnotempty("") == false
```

## <a name="syntax"></a>Синтаксис

`isnotempty(`[*значение*]`)`

`notempty(`[*значение*] `)` --псевдоним `isnotempty`

## <a name="example"></a>Пример

```kusto
T
| where isnotempty(fieldName)
| count
```
