---
title: раунд() - Исследователь данных Azure (ru) Документы Майкрософт
description: Эта статья описывает круглую () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05111b6261c14f21d8d08e88a4c070faab32dc4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510240"
---
# <a name="round"></a>round()

Возвращает закругленный источник в указанную точность.

**Синтаксис**

`round(`*источник* `,` - *Точность*`)`

**Аргументы**

* *источник*: Источник масштабирования раунда рассчитывается на.
* *Точность*: Количество цифр, к источникам будет округлено. (значение по умолчанию 0)

**Возвращает**

Закругленный источник с указанной точностью.

Раунд отличается [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) от того, что первые раунды число к определенному числу цифр в то время как значение последних раундов в целый размер множественного данного размера бена (круглый (2,15, 1) возвращает 2,2 в то время как бен (2,15, 1) возвращает 2).
 

**Примеры**

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```