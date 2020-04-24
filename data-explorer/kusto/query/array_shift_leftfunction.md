---
title: array_shift_left () - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает array_shift_left () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 5b623bdcccc75261d0fb9324bb6015e16b0ff9b8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518791"
---
# <a name="array_shift_left"></a>array_shift_left()

`array_shift_left()`сдвиги значений внутри массива влево.

**Синтаксис**

`array_shift_left(`*arr*, *shift_count* , *fill_value*`)`

**Аргументы**

* *arr*: Ввод массива разделить, должен быть динамический массив.
* *shift_count*: Integer, определяющий количество позиций, которые элементы массива будут смещены влево. Если значение отрицательное, элементы будут сдвинуты вправо.
* *fill_value*: значение Scalar, которое используется для вставки элементов вместо тех, которые были сдвинуты и удалены. По умолчанию: нулевая стоимость или пустая строка (в зависимости от типа *arr).*

**Возвращает**

Динамический массив, содержащий такое же количество элементов, как и в исходном массиве, где каждый элемент был смещен в соответствии с *shift_count.* Новые элементы, которые добавляются вместо элементов, которые удаляются будет иметь значение *fill_value.*

**См. также:**

* Для правильного смещения массива см., [array_shift_right()](array_shift_rightfunction.md).
* Для правильного вращения массива см. [array_rotate_right()](array_rotate_rightfunction.md).
* Для вращающегося массива слева см. [array_rotate_left()](array_rotate_leftfunction.md).

**Примеры**

* Сдвиг влево на две позиции:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|3,4,5,null, null|

* Сдвиг влево двумя позициями и добавление значения по умолчанию:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, 2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[3,4,5,-1,-1]|


* Сдвиг вправо на две позиции с использованием отрицательного *shift_count* значения:

    ```kusto
    print arr=dynamic([1,2,3,4,5]) 
    | extend arr_shift=array_shift_left(arr, -2, -1)
    ```
    
    |Arr|arr_shift|
    |---|---|
    |[1,2,3,4,5]|[-1,-1,1,2,3]|
