---
title: isnotnull () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается isnotnull () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ff472919ecda9550e7e0bcd6b403c605d029bfb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347193"
---
# <a name="isnotnull"></a>isnotnull()

Возвращает, `true` Если аргумент не равен null.

## <a name="syntax"></a>Синтаксис

`isnotnull(`[*значение*]`)`

`notnull(`[*значение*] `)` -alias для`isnotnull`

## <a name="example"></a>Пример

```kusto
T | where isnotnull(PossiblyNull) | count
```

Обратите внимание, что того же эффекта можно добиться другими способами:

```kusto
T | summarize count(PossiblyNull)
```