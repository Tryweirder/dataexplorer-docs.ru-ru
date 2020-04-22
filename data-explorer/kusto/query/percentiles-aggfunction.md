---
title: процентильных (), процентилей() - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны процентильные (), процентильные материалы () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ecbb56305cfc43033ca172071f48b25768de6d2f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663661"
---
# <a name="percentile-percentiles"></a>процентильных (), процентилей()

Возвращает смету для указанного [ближайшего ранга процентиль](#nearest-rank-percentile) населения, определяемого *Expr*. Точность зависит от плотности заполнения области процентиля. Эта функция может быть использована только в контексте агрегации внутри [суммировать](summarizeoperator.md)

* `percentiles()`как, `percentile()`но вычисляет ряд процентильных значений (что быстрее, чем расчет каждого процентиля по отдельности).
* `percentilesw()`как, `percentilew()`но вычисляет ряд взвешенных значения процентильных (что быстрее, чем расчет каждого процентиля по отдельности).
* `percentilew()`и `percentilesw()` позволяет вычислять взвешенные процентилы. Взвешенные процентивы вычисляют данные процентилы "взвешенным" способом `Weight` - рассматривая каждое значение так, как если бы оно повторялось раз в входе.

**Синтаксис**

резюмировать `percentile(` *Expr* `,` *Процентиль*`)`

суммировать `percentiles(` *Expr* `,` *Percentile1* -`,` *Процентиль2*`)`

суммировать `percentiles_array(` *Expr* `,` *Percentile1* -`,` *Процентиль2*`)`

обобщить `percentiles_array(`массив *Expr* `,` *Dynamic*`)`

резюмировать `percentilew(` *Expr* `,` *WeightExpr* `,` *Процентиль*`)`

суммировать `percentilesw(` *Expr* `,` *WeightExpr* `,` `,` *Процентиль1* - *Процентиль2*`)`

суммировать `percentilesw_array(` *Expr* `,` *WeightExpr* `,` `,` *Процентиль1* - *Процентиль2*`)`

обобщить `percentilesw_array(` *Expr* `,` *WeightExpr* `,` *Динамический массив*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации.
* *WeightExpr*: Выражение, которое будет использоваться в качестве веса значений для расчета агрегации.
* *Процентиль* является двойной константой, которая определяет процентиль.
* *Динамический массив*: список процентилей в динамическом массиве целых или плавающих точек чисел.

**Возвращает**

Возвращает оценку для *Expr* указанных процентилей в группе. 

**Примеры**

Значение `Duration` этого составляет более 95% от набора выборки и меньше 5% от набора выборки:

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

Одновременно вычислите 5, 50 (средний) и 95:

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/aggregations/percentiles.png" alt-text="Процентили":::

Результаты показывают, что в Европе 5% звонков короче 11,55, 50% звонков короче 3 минут, 18,46 секунды, а 95% звонков короче 40 минут 48 секунд.

Вычисление множественной статистики:

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>Взвешенные процентили

Предположим, что вы измеряете время (Длительность), необходимое для выполнения действия снова и снова. Вместо записи каждого значения измерения, вы конденсируете их, записывая каждое значение Продолжительности, округленное до 100 мсек и сколько раз, что округлое значение появилось (BucketSize).

Вы можете `summarize percentilesw(Duration, BucketSize, ...)` использовать для расчета данных процентилей в "взвешенный" способ - лечение каждого значения Продолжительности, как если бы он был повторен BucketSize раз в ввода, без фактического материализировать эти записи.

Пример: Клиент имеет набор значений задержки в `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`миллисекундах: .

Чтобы уменьшить пропускную способность и хранение, выполните предварительную `{ 10, 20, 30, 40, 50, 100 }`агрегацию к следующим ведрам: и подсчитайте количество событий в каждом ведре, которое дает следующую таблицу Kusto:

:::image type="content" source="images/aggregations/percentilesw-table.png" alt-text="Таблица Процентилесв":::

Что можно прочитать как:
 * 8 событий в ведре 10 `{ 1, 1, 2, 2, 2, 5, 7, 7 }`мс (соответствует подмножеству)
 * 6 событий в 20мс ведро `{ 12, 12, 15, 15, 15, 18 }`(соответствует подмножество )
 * 3 события в ведре 30 `{ 21, 22, 26 }`мс (соответствует подмножеству)
 * 1 событие в 40мс ведро `{ 35 }`(соответствует подмножество )

На данный момент исходные данные больше не доступны, и все, что у нас есть, это количество событий в каждом ведре. Для вычисления процентилей из этих `percentilesw()` данных используйте эту функцию. Например, для 50, 75 и 99,9 процентилей используйте следующий запрос: 

```kusto
datatable (ReqCount:long, LatencyBucket:long) 
[ 
    8, 10, 
    6, 20, 
    3, 30, 
    1, 40 
]
| summarize percentilesw(LatencyBucket, ReqCount, 50, 75, 99.9) 
```

Результатом для вышеуказанного запроса является:

:::image type="content" source="images/aggregations/percentilesw-result.PNG" alt-text="Результат Percentilesw":::

Обратите внимание, что указанный запрос `percentiles(LatencyBucket, 50, 75, 99.9)` соответствует функции, если данные были израсходовано на следующую форму:

:::image type="content" source="images/aggregations/percentilesw-rawtable.png" alt-text="Percentilesw сырой стол":::

## <a name="getting-multiple-percentiles-in-an-array"></a>Получение нескольких процентилей в массиве

Несколько процентилей можно получить в виде массива в одном динамическом столбце вместо нескольких столбцов: 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/aggregations/percentiles-array-result.png" alt-text="Результат массива процентов":::

Аналогичным образом, взвешенные процентилы могут быть возвращены в качестве динамического массива с использованием`percentilesw_array`

Проценты для `percentiles_array` `percentilesw_array` и могут быть указаны в динамическом массиве целых или плавающих точек чисел. Массив должен быть постоянным, но не должен быть буквальным.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>Ближайший процентиль ранга
*P-th*процентиль (0 < *P* <100) из списка упорядоченных значений (отсортированных от наименьшего до наибольшего) является наименьшим значением в списке таков, что *P* процент данных меньше или равна этому значению[(из статьи Википедии о процентилях](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method))

Определите *0-й*процентилы, чтобы быть самым маленьким членом населения.

>[!NOTE]
> Учитывая приближенный характер расчета, фактическое возвращенное значение не может быть членом популяции.
> Определение ближайшего ранга означает, что *P*50 не соответствует [интерполятивному определению медианы.](https://en.wikipedia.org/wiki/Median) При оценке значимости этого расхождения для конкретного приложения следует учитывать численность населения и [ошибку в оценке.](#estimation-error-in-percentiles)

## <a name="estimation-error-in-percentiles"></a>Ошибка оценки в процентилях

Статистическая оценка процентилей предоставляет приблизительное значение с помощью [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

>[!NOTE]
> * Границы ошибки оценки изменяются со значением запрошенного процентиля. Лучшая точность находится на концах шкалы 0..100. Проценты 0 и 100 являются точными минимальными и максимальными значениями распределения. Точность постепенно снижается к середине шкалы. Это худший в среднем и ограничен на 1%. 
> * Границы ошибки наблюдаются на диапазоне, а не на значении. Предположим, что процентиль (X, 50) вернул значение Xm. Оценка гарантирует, что по крайней мере 49% и не более 51% значений X меньше или равно Xm. Нет теоретического ограничения на разницу между Xm и фактическим средним значением X.
> * Оценка иногда может привести к точному значению, но нет надежных условий для определения того, когда это произойдет.