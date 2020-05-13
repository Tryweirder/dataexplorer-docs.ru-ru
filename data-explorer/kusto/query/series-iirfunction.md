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
ms.openlocfilehash: 8b03970aacafef932f6397e64afdf871dc086bc1
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372631"
---
# <a name="series_iir"></a>series_iir()

Применяет фильтр бесконечного ответа для ряда.  

Принимает выражение, содержащее динамический числовой массив в качестве входных данных и применяет [бесконечный фильтр ответов](https://en.wikipedia.org/wiki/Infinite_impulse_response) с недопустимым импульсом. Указав коэффициенты фильтра, можно использовать, например, для вычисления совокупной суммы ряда, применения операций сглаживания, а также различных фильтров [высокого уровня, многоэтапного](https://en.wikipedia.org/wiki/High-pass_filter)и [нижнего](https://en.wikipedia.org/wiki/Low-pass_filter) [этапа.](https://en.wikipedia.org/wiki/Band-pass_filter) Функция принимает в качестве входных данных столбец, содержащий динамический массив, и два статических динамических массива коэффициентов *a* и *b* фильтра и применяют фильтр к столбцу. Она выводит новый столбец динамического массива, содержащий отфильтрованные результаты.  
 

**Синтаксис**

`series_iir(`*x* `,` *b* `,` *a*`)`

**Аргументы**

* *x*: ячейка динамического массива, которая представляет собой массив числовых значений, обычно результирующий результат операторов [make-Series](make-seriesoperator.md) или [make_list](makelist-aggfunction.md) .
* *б*: константное выражение, содержащее коэффициенты числителя фильтра (хранимые в виде динамического массива числовых значений).
* *а*. константное выражение, например *b*. Содержит коэффициенты знаменателя фильтра.

> [!IMPORTANT]
> Первый элемент `a` (т. е. `a[0]` ) не должны быть нулем (чтобы избежать деления на 0; см. формулу ниже).

**Дополнительные сведения о рекурсивной формуле фильтра**

* Учитывая входной массив X и коэффициенты, а также массивы a, b длин n_a и n_b соответственно, функция пересылки фильтра, создающая выходной массив Y, определяется (см. также в Википедии).

<div align="center">
Y<sub>i</sub> =<sub>0</sub><sup>– 1</sup>(b<sub>0</sub>x<sub>i</sub> 
 + б b<sub>1</sub>X<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>y-<sub>1</sub>– a<sub>2</sub>y<sub>i-2</sub> - )<sub><sub>a</sub></sub><sub><sub>a</sub></sub>
</div>

**Пример**

Вычисление совокупной суммы может выполняться фильтром IIR с коэффициентами *a*= [1,-1] и *b*= [1]:  

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
|4,0|10,0|

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
