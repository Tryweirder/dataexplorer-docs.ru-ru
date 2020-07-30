---
title: полугодие () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается г. () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 032cc319661218e77d5b23e6c649de7d5856d6c9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347652"
---
# <a name="getyear"></a>getyear()

Возвращает часть года для `datetime` аргумента.

## <a name="example"></a>Пример

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```