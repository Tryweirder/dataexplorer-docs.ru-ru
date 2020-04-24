---
title: повтор() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается повтор () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fd944112a64e7400e38c627b7b0651bb6fccd54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510427"
---
# <a name="repeat"></a>repeat()

Создает динамический массив, вмещающих ряд равных значений.

**Синтаксис**

`repeat(`*value*`,` *количество* значений`)` 

**Аргументы**

* *значение*: Значение элемента в результирующем массиве. Тип *значения* может быть boolean, целый teger, длинный, реальный, дата, или timespan.   
* *отсчет*: Количество элементов в результирующем массиве. *Граф* должен быть целым числом.
Если *количество* равно нулю, возвращается пустой массив.
Если *количество* меньше нуля, возвращается нулевая стоимость. 

**Примеры**

В следующем примере возвращается `[1, 1, 1]`.

```kusto
T | extend r = repeat(1, 3)
```