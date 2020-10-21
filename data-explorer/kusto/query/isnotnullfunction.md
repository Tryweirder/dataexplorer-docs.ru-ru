---
title: isnotnull () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается isnotnull () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c198bd34161c683c6e5c6f1bde2990c0605122c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253266"
---
# <a name="isnotnull"></a>isnotnull()

Возвращает, `true` Если аргумент не равен null.

## <a name="syntax"></a>Синтаксис

`isnotnull(`[*значение*]`)`

`notnull(`[*значение*] `)` -alias для `isnotnull`

## <a name="example"></a>Пример

```kusto
T | where isnotnull(PossiblyNull) | count
```

Обратите внимание, что того же эффекта можно добиться другими способами:

```kusto
T | summarize count(PossiblyNull)
```