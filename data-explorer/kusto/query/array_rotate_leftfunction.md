---
title: array_rotate_left () — обозреватель данных Azure
description: В этой статье описывается array_rotate_left () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7c9e6a318ebcd0a0911b3a86c1bf0ad99aeb2f51
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248127"
---
# <a name="array_rotate_left"></a>array_rotate_left()

Поворачивает значения внутри `dynamic` массива влево.

## <a name="syntax"></a>Синтаксис

`array_rotate_left(`*arr*, *rotate_count*`)`

## <a name="arguments"></a>Аргументы

* *arr*: входной массив для разбиения должен быть динамическим массивом.
* *rotate_count*: целое число, указывающее количество позиций, на которое будут поворачиваться элементы массива слева. Если значение отрицательное, элементы будут повернуты вправо.

## <a name="returns"></a>Результаты

Динамический массив, содержащий тот же объем элементов, что и в исходном массиве, где каждый элемент был повернут в соответствии с *rotate_count*.

## <a name="see-also"></a>См. также раздел

* Сведения о повороте массива справа см. в разделе [array_rotate_right ()](array_rotate_rightfunction.md).
* Сведения о перемещении массива влево см. в разделе [array_shift_left ()](array_shift_leftfunction.md).
* Сведения о перемещении массива вправо см. в разделе [array_shift_right ()](array_shift_rightfunction.md).

## <a name="examples"></a>Примеры

* Поворот влево на две позиции:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, 2)
    ```
    
    |маленькая|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5, 1, 2]|

* Поворот вправо на две позиции с использованием отрицательного rotate_count значения:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_rotated=array_rotate_left(arr, -2)
    ```
    
    |маленькая|arr_rotated|
    |---|---|
    |[1, 2, 3, 4, 5]|[4, 5, 1, 2, 3]|