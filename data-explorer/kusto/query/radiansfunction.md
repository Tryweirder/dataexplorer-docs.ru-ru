---
title: radians () - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны радианы () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0dc04c5f9593b6bd5fc61f57d20819cf7d2a178c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510665"
---
# <a name="radians"></a>radians()

Преобразует значение угла в градусах в значение в радианах, используя формулу`radians = (PI / 180 ) * angle_in_degrees`

**Синтаксис**

`radians(`*a*`)`

**Аргументы**

* *a*: Угол в градусах (реальное число).

**Возвращает**

* Соответствующий угол в радиане для угла, указанного в градусах. 

**Примеры**

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|