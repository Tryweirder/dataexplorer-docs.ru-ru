---
title: array_rotate_right () — обозреватель данных Azure
description: В этой статье описывается array_rotate_right () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 23f16885d1988823fe2b301035c6ba5d54471136
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225774"
---
# <a name="array_rotate_right"></a>array_rotate_right()

`array_rotate_right()`поворачивает значения внутри массива вправо.

**Синтаксис**

`array_rotate_right(`*arr*, *rotate_count*`)`

**Аргументы**

* *arr*: входной массив для разбиения должен быть динамическим массивом.
* *rotate_count*: целое число, указывающее количество позиций, на которое элементы массива будут повернуты вправо. Если значение отрицательное, элементы будут повернуты влево.

**Возвращает**

Динамический массив, содержащий тот же объем элементов, что и в исходном массиве, где каждый элемент был повернут в соответствии с *rotate_count*.

**См. также:**

* Сведения о повороте массива влево см. в разделе [array_rotate_left ()](array_rotate_leftfunction.md).
* Сведения о перемещении массива влево см. в разделе [array_shift_left ()](array_shift_leftfunction.md).
* Сведения о перемещении массива вправо см. в разделе [array_shift_right ()](array_shift_rightfunction.md).

**Примеры**

* Поворот вправо на две позиции:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, 2)
    ```
    
    |маленькая|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[4, 5, 1, 2, 3]|

* Поворот влево на две позиции с использованием отрицательного rotate_count значения:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_right(arr, -2)
    ```
    
    |маленькая|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, 1, 2]|
