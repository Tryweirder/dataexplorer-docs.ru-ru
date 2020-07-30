---
title: array_shift_right () — обозреватель данных Azure
description: В этой статье описывается array_shift_right () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 866f7865abe2debeab22bfbf13bc7641d9bc5078
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349624"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`Сдвигает значения в массиве вправо.

## <a name="syntax"></a>Синтаксис

`array_shift_right(`*`arr`*, *`shift_count`* [, *`fill_value`* ]`)`

## <a name="arguments"></a>Аргументы

* *`arr`*: Входной массив для разбиения должен быть динамическим массивом.
* *`shift_count`*: Целое число, указывающее количество позиций, на которое элементы массива будут перемещены вправо. Если значение отрицательное, элементы будут перемещены влево.
* *`fill_value`*: скалярное значение, используемое для вставки элементов вместо тех, которые были сдвинуты и удалены. Значение по умолчанию: NULL или пустая строка (в зависимости от типа *arr* ).

## <a name="returns"></a>Результаты

Динамический массив, содержащий тот же объем элементов, что и в исходном массиве. Каждый элемент был перемещен в соответствии с *`shift_count`* . Новые элементы, добавляемые вместо удаленных элементов, будут иметь значение *`fill_value`* .

**См. также:**

* Сведения для сдвига массива Left см. в разделе [array_shift_left ()](array_shift_leftfunction.md).
* Сведения о повороте массива вправо см. в разделе [array_rotate_right ()](array_rotate_rightfunction.md).
* Сведения о повороте массива Left см. в разделе [array_rotate_left ()](array_rotate_leftfunction.md).

## <a name="examples"></a>Примеры

* Сдвиг вправо на две позиции:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |маленькая|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[null, null, 1, 2, 3]|

* Сдвиг вправо на две позиции и добавление значения по умолчанию:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |маленькая|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[-1,-1, 1, 2, 3]|

* Сдвиг влево на две позиции с использованием отрицательного shift_count значения:

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |маленькая|arr_shift|
    |---|---|
    |[1, 2, 3, 4, 5]|[3, 4, 5,-1,-1]|
    