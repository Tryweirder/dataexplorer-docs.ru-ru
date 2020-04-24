---
title: array_rotate_right () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны array_rotate_right () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4f45db14f6ca2fe990f8d139c608ebed1915aa16
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518808"
---
# <a name="array_rotate_right"></a>array_rotate_right()

`array_rotate_right()`вращает значения внутри массива справа.

**Синтаксис**

`array_rotate_right(`*arr*, *rotate_count*`)`

**Аргументы**

* *arr*: Ввод массива разделить, должен быть динамический массив.
* *rotate_count*: Integer, определяющий количество позиций, которые элементы массива будут повернуты вправо. Если значение отрицательное, элементы будут повернуты влево.

**Возвращает**

Динамический массив, содержащий такое же количество элементов, как и в исходном массиве, где каждый элемент был повернут в соответствии с *rotate_count.*

**См. также:**

* Для вращающегося массива влево см. [array_rotate_left()](array_rotate_leftfunction.md).
* Для перемещения массива влево см. [array_shift_left()](array_shift_leftfunction.md).
* Для смещения массива вправо см. [array_shift_right()](array_shift_rightfunction.md).

**Примеры**

* Поворот вправо на две позиции:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[4,5,1,2,3]|

* Поворот влево двумя позициями с использованием отрицательного rotate_count значения:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |Arr|arr_rotated|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,1,2]|