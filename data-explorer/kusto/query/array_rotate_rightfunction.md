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
ms.openlocfilehash: 267e2b4d5769efee87dd90b9a3265310998fcbd9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712108"
---
# <a name="array_rotate_right"></a>array_rotate_right()

Поворачивает значения внутри `dynamic` массива вправо.

## <a name="syntax"></a>Синтаксис

`array_rotate_right(`*arr*, *rotate_count*`)`

## <a name="arguments"></a>Аргументы

* *arr*: входной массив для разбиения должен быть динамическим массивом.
* *rotate_count*: целое число, указывающее количество позиций, на которое элементы массива будут повернуты вправо. Если значение отрицательное, элементы будут повернуты влево.

## <a name="returns"></a>Возвращает

Динамический массив, содержащий тот же объем элементов, что и в исходном массиве, где каждый элемент был повернут в соответствии с *rotate_count*.

## <a name="see-also"></a>См. также раздел

* Сведения о повороте массива влево см. в разделе [array_rotate_left ()](array_rotate_leftfunction.md).
* Сведения о перемещении массива влево см. в разделе [array_shift_left ()](array_shift_leftfunction.md).
* Сведения о перемещении массива вправо см. в разделе [array_shift_right ()](array_shift_rightfunction.md).

## <a name="examples"></a>Примеры

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
