---
title: array_iif () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны array_iif () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: f99b9aa8a9d081a7f28cd2e5bb8750b15f2fcdac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663912"
---
# <a name="array_iif"></a>array_iif()

Элемент-мудрый iif функции на динамических массивах.

Другой псевдоним: array_iff().

**Синтаксис**

`array_iif(`*ConditionArray*, *IfTrue*, *Еслиложное*`)`

**Аргументы**

* *conditionArray*: Ввод *массива boolean* или числовые значения, должен быть динамическим массивом.
* *еслиTrue*: Ввод массива значений или примитивное значение - значение результата (ы), когда соответствующее значение *ConditionArray* *верно.*
* *еслиFalse*: Ввод массива значений или примитивное значение - значение результата (ы), когда соответствующее значение *ConditionArray* *является ложным.*

**Примечания**

* Длина результата - длина *conditionArray*.
* Численное значение состояния рассматривается *0*как *состояние* !
* Значение нечиславного/нулевых состояний будет иметь нулевой уличенный в соответствующем индексе результата.
* Отсутствующие значения (в массивах более короткой длины) рассматриваются как нулевые.

**Возвращает**

Динамический массив значений, взятых либо из *значений IfTrue,* либо *IfFalse,* в соответствии с соответствующим значением массива состояния.

**Пример**

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|(истинно, ложно, правда)|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
