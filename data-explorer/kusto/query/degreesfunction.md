---
title: градусов () - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны степени () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1365d6c6629f4f4769d7c4b62491eaec25e4ec59
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516037"
---
# <a name="degrees"></a>degrees()

Преобразует значение угла в радианах в значение в градусах, используя формулу`degrees = (180 / PI ) * angle_in_radians`

**Синтаксис**

`degrees(`*a*`)`

**Аргументы**

* *a*: Угол в радиане (реальное число).

**Возвращает**

* Соответствующий угол в градусах для угла, указанного в радиане. 

**Примеры**

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|градусов0|градусов1|градусов2|
|---|---|---|
|45|270|0|
