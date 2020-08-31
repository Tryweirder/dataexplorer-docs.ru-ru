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
ms.openlocfilehash: 9f7811c2668bd0bb2d6e3711800ee5d9a450b9ec
ms.sourcegitcommit: 487485c87706183a9824f695e5b56b0bc5ade048
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89056240"
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
