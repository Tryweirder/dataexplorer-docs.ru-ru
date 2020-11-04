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
ms.openlocfilehash: 44161dcdc34232225f4b133fe0a569fb818c3f0f
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349381"
---
# <a name="isnotnull"></a>isnotnull()

Возвращает, `true` Если аргумент не равен null.

## <a name="syntax"></a>Синтаксис

`isnotnull(`[ *значение* ]`)`

`notnull(`[ *значение* ] `)` -alias для `isnotnull`

## <a name="example"></a>Пример

```kusto
T | where isnotnull(PossiblyNull) | count
```
