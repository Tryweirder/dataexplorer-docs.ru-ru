---
title: array_shift_right () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны array_shift_right () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 73f87d4c5ce1a841404e438e0e5647089b38785f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518774"
---
# <a name="array_shift_right"></a>array_shift_right()

`array_shift_right()`сдвиги значений внутри массива вправо.

**Синтаксис**

`array_shift_right(`*arr*, *shift_count* , *fill_value*`)`

**Аргументы**

* *arr*: Ввод массива разделить, должен быть динамический массив.
* *shift_count*: Integer, определяющий количество позиций, которые элементы массива будут смещены вправо. Если значение отрицательное, элементы будут сдвинуты влево.
* *fill_value:* значение масштабирования, которое используется для вставки элементов вместо тех, которые были сдвинуты и удалены. По умолчанию: нулевая стоимость или пустая строка (в зависимости от типа *arr).*

**Возвращает**

Динамический массив, содержащий такое же количество элементов, как и в исходном массиве, где каждый элемент был смещен в соответствии с *shift_count.* Новые элементы, которые добавляются вместо элементов, которые удаляются будет иметь значение *fill_value.*

**См. также:**

* Для смещения массива слева см. [array_shift_left()](array_shift_leftfunction.md).
* Для правильного вращения массива см. [array_rotate_right()](array_rotate_rightfunction.md).
* Для вращающегося массива слева см. [array_rotate_left()](array_rotate_leftfunction.md).

**Примеры**

* Сдвиг вправо на две позиции:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|(null,null,1,2,3)|

* Сдвиг вправо двумя позициями и добавление значения по умолчанию:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, 2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[-1,-1,1,2,3]|


* Сдвиг влево двумя позициями с использованием отрицательного shift_count значения:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_right(arr, -2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,-1,-1]|