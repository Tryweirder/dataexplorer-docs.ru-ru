---
title: полугодие () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается г. () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a820d05b453be7cc991f8068644d33ff990115fc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251145"
---
# <a name="getyear"></a>getyear()

Возвращает часть года для `datetime` аргумента.

## <a name="example"></a>Пример

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```