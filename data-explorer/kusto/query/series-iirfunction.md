---
title: series_iir () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается series_iir () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 96452265e07a8a8b057dc70bec520be6ab298dd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508302"
---
# <a name="series_iir"></a>series_iir()

Применяет фильтр Infinite Impulse Response в серии.  

Принимает выражение, содержащее динамический числовой массив, в качестве ввода и применяет фильтр [Infinite Impulse Response.](https://en.wikipedia.org/wiki/Infinite_impulse_response) Указывая коэффициенты фильтра, его можно использовать, например, для расчета совокупной суммы серии, для применения сглаживающих операций, а также различных [фильтров с высоким проходом,](https://en.wikipedia.org/wiki/High-pass_filter) [диапазона и](https://en.wikipedia.org/wiki/Band-pass_filter) [низкопроходимых](https://en.wikipedia.org/wiki/Low-pass_filter) фильтров. Функция принимает в качестве ввода столбца, содержащего динамический массив и два статических динамических массива коэффициентов *a* и *b* фильтра, и применяет фильтр на столбце. Она выводит новый столбец динамического массива, содержащий отфильтрованные результаты.  
 

**Синтаксис**

`series_iir(`*x* `,` *b* `,` *a*`)`

**Аргументы**

* *x*: Динамическая ячейка массива, представляющая собой массив числовых значений, как правило, полученная выходная часть [изсерий или](make-seriesoperator.md) [make_list](makelist-aggfunction.md) операторов.
* *b*: Постоянное выражение, содержащее коэффициенты числителя фильтра (хранится как динамический массив числовых значений).
* *a*: Постоянное выражение, как *b*. Содержит коэффициенты знаменателя фильтра.

> [!IMPORTANT]
> Первый элемент `a` (т.е.) `a[0]`не должен быть нулевым (чтобы избежать деления на 0; см. формулу ниже).

**Подробнее о рекурсивной формуле фильтра**

* С учетом массива ввода X и коэффициентов a, b длин n_a и n_b соответственно, функция передачи фильтра, генерирующая выходный массив Y, определяется (см. также в Википедии):

<div align="center">
Y<sub>i</sub> q a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub><sub><sub>b</sub></sub> X<sub>i-1</sub> + ...<sub>1</sub><sub>i-2</sub> - <sub>i-1</sub><sub><sub>b</sub></sub><sub><sub>a</sub></sub><sub><sub>a</sub></sub><sub>2</sub>
 -
</div>

**Пример**

Расчет кумулятивной суммы может быть выполнен с помощью iir-фильтра с коэффициентами *1,-1*и *b*  

```kusto
let x = range(1.0, 10, 1);
print x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mv-expand x, y
```

| x | y |
|:--|:--|
|1.0|1.0|
|2.0|3.0|
|3.0|6,0|
|4,0|10,0|

Вот как обернуть его в функцию:

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