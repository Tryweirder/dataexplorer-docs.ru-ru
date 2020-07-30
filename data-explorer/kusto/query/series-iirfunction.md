---
title: series_iir () — обозреватель данных Azure
description: В этой статье описывается series_iir () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: e1b863b83e08fae680e1a387ca2fdd2a93d111a8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351443"
---
# <a name="series_iir"></a>series_iir()

Применяет фильтр бесконечного ответа для ряда.  

Функция принимает выражение, содержащее динамический числовой массив, в качестве входных данных и применяет [бесконечный фильтр ответов](https://en.wikipedia.org/wiki/Infinite_impulse_response) с недопустимым импульсом. Указав коэффициенты фильтра, можно использовать функцию:
* Вычисление совокупной суммы для ряда
* Применение операций сглаживания
* применение различных фильтров с [высоким уровнем прохода](https://en.wikipedia.org/wiki/High-pass_filter), с [полосой](https://en.wikipedia.org/wiki/Band-pass_filter)до и с низкой степенью [прохождения](https://en.wikipedia.org/wiki/Low-pass_filter)

Функция принимает в качестве входных данных столбец, содержащий динамический массив, и два статических динамических массива коэффициентов *a* и *b* фильтра и применяют фильтр к столбцу. Она выводит новый столбец динамического массива, содержащий отфильтрованные результаты.  

## <a name="syntax"></a>Синтаксис

`series_iir(`*x* `,` *b* `,` *a*`)`

## <a name="arguments"></a>Аргументы

* *x*: ячейка динамического массива, которая представляет собой массив числовых значений, обычно результирующий результат операторов [make-Series](make-seriesoperator.md) или [make_list](makelist-aggfunction.md) .
* *б*: константное выражение, содержащее коэффициенты числителя фильтра (хранимые в виде динамического массива числовых значений).
* *а*. константное выражение, например *b*. Содержит коэффициенты знаменателя фильтра.

> [!IMPORTANT]
> Первый элемент (т. е. `a` `a[0]` ) не должны быть нулевым, чтобы избежать деления на 0. См. [формулу ниже](#the-filters-recursive-formula).

## <a name="the-filters-recursive-formula"></a>Рекурсивная Формула фильтра

* Рассмотрите входной массив X и коэффициенты, а затем массивы a и b с длиной n_a и n_b соответственно. Функция перемещения фильтра, который создаст выходной массив Y, определяется следующим образом:

<div align="center">
Y<sub>i</sub> =<sub>0</sub><sup>– 1</sup>(b<sub>0</sub>x<sub>i</sub> 
 + б b<sub>1</sub>X<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>y-<sub>1</sub>– a<sub>2</sub>y<sub>i-2</sub> - )<sub><sub>a</sub></sub><sub><sub>a</sub></sub>
</div>

## <a name="example"></a>Пример

Вычислите совокупную сумму. Используйте фильтр IIR с коэффициентами *a*= [1,-1] и *b*= [1]:  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let x = range(1.0, 10, 1);
print x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mv-expand x, y
```

| x | y |
|:--|:--|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10.0|

Вот как обернуть его в функцию:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|d            |дд  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|
