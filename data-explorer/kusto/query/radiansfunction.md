---
title: радианы () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается радианы () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3aaa41a631498e2938acf722b75f409a1bbe5031
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345952"
---
# <a name="radians"></a>radians()

Преобразует значение угла в градусах в значение в радианах, используя формулу`radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>Синтаксис

`radians(`*конкретного*`)`

## <a name="arguments"></a>Аргументы

* *а*: угол в градусах (вещественное число).

## <a name="returns"></a>Результаты

* Соответствующий угол в радианах для угла, указанного в градусах. 

## <a name="examples"></a>Примеры

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1,5707963267949|3,14159265358979)|6.28318530717959|