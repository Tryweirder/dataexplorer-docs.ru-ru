---
title: Анализ данных временных рядов с помощью Azure Data Explorer
description: Узнайте, как анализировать данные временных рядов в облаке с помощью Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/07/2019
ms.localizationpriority: high
ms.openlocfilehash: d7046b8767413889bc77240032a2292deb7c89a3
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513001"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Анализ временных рядов в службе Azure Data Explorer

Служба Azure Data Explorer (ADX) выполняет непрерывный сбор данных телеметрии из облачных служб или с устройств Интернета вещей. Эти данные можно анализировать для получения различных аналитических сведений, например мониторинга работоспособности служб, физических производственных процессов и тенденций использования. Анализ выполняется по временным рядам выбранных метрик для нахождения отклонений в закономерности по сравнению с типичной базовой закономерностью.
ADX содержит встроенную поддержку создания, обработки и анализа различных временных рядов. В этой статье описано, как ADX используется для создания и анализа **тысяч временных рядов за несколько секунд**, что позволяет реализовать решения и рабочие процессы для мониторинга практически в режиме реального времени.

## <a name="time-series-creation"></a>Создание временных рядов

В этом разделе мы создадим большой набор обычных временных рядов простым и интуитивно понятным способом — с помощью оператора `make-series` — и заполним недостающие значения по необходимости.
Первый этап анализа временных рядов — секционировать и преобразовать исходную таблицу данных телеметрии в набор временных рядов. В этой таблице обычно содержатся столбец меток времени, контекстно-зависимые измерения и необязательные метрики. Измерения используются для секционирования данных. Цель — создание тысяч временных рядов в каждой секции через регулярные интервалы времени.

Входная таблица *demo_make_series1* содержит 600 тысяч записей произвольного трафика веб-служб. С помощью приведенной ниже команды создайте выборку из 10 записей:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

Результатом будет таблица со столбцом меток времени, тремя столбцами контекстно-зависимых измерений и без метрик:

| TimeStamp | BrowserVer (версия браузера) | OsVer (версия ОС) | Страна или регион |
| --- | --- | --- | --- |
| 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Соединенное Королевство |
| 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
| 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Соединенное Королевство |
| 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Соединенное Королевство |
| 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Литовская Республика |
| 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Индия |
| 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Соединенное Королевство |
| 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Нидерланды |
| 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Соединенное Королевство |
| 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Индия |

Из-за отсутствия метрик мы можем сформировать только набор временных рядов, представляющий сам объем трафика и секционированный по ОС, выполнив следующий запрос:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- С помощью оператора [`make-series`](kusto/query/make-seriesoperator.md) создайте набор из трех временных рядов, где:
    - `num=count()`: временной ряд трафика.
    - `range(min_t, max_t, 1h)`: временной ряд создается по 1-часовым ячейкам в указанном диапазоне времени (самые старые и самые новые метки времени записей таблицы).
    - `default=0`: укажите метод заполнения отсутствующих ячеек для создания регулярных временных рядов. Также можно использовать [`series_fill_const()`](kusto/query/series-fill-constfunction.md), [`series_fill_forward()`](kusto/query/series-fill-forwardfunction.md), [`series_fill_backward()`](kusto/query/series-fill-backwardfunction.md) и [`series_fill_linear()`](kusto/query/series-fill-linearfunction.md) для изменений.
    - `byOsVer`: секционирование по ОС.
- Фактическая структура данных во временных рядах представляет собой числовой массив агрегированных значений по каждой ячейке времени. Мы используем `render timechart` для визуализации.

В приведенной выше таблице есть три секции. Мы можем создать отдельные временные ряды для каждой версии операционной системы — Windows 10 (красный), 7 (синий) и 8.1 (зеленый), как показано на графике:

![Секция временных рядов](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Функции анализа временных рядов

В этом разделе мы выполним типичные функции обработки рядов.
После создания набора временных рядов ADX поддерживает постоянно обновляемый список функций для их обработки и анализа, которые можно найти в [документации по временным рядам](kusto/query/machine-learning-and-tsa.md). Опишем несколько репрезентативных функций для обработки и анализа временных рядов.

### <a name="filtering"></a>Фильтрация

Фильтрация — это распространенная практика при обработке сигналов, которая полезна в задачах обработки временных рядов (например, для сглаживания сигнала с шумами, обнаружения изменений).
- Существует две универсальные функции фильтрации.
    - [`series_fir()`](kusto/query/series-firfunction.md): Применение фильтра FIR. Используется для простого вычисления скользящего среднего и дифференциации временных рядов для обнаружения изменений.
    - [`series_iir()`](kusto/query/series-iirfunction.md): Применение фильтра IIR. Используется для экспоненциального сглаживания и вычисления кумулятивной суммы.
- Можно расширить временной ряд функцией `Extend` путем добавления в запрос нового ряда скользящих средних с размером в 5 ячеек (с именем *ma_num*):

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Фильтрация временных рядов](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Регрессионный анализ

ADX поддерживает сегментированный анализ линейной регрессии для оценки тенденций временного ряда.
- Функция [series_fit_line()](kusto/query/series-fit-linefunction.md) позволяет подобрать оптимальную линию для временного ряда с целью определения общей тенденции.
- Функция [series_fit_2lines()](kusto/query/series-fit-2linesfunction.md) позволяет обнаруживать изменения тенденций относительно базовой линии, которые важны в сценариях мониторинга.

Пример использования функций `series_fit_line()` и `series_fit_2lines()` в запросе по временному ряду:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Регрессия временного ряда](media/time-series-analysis/time-series-regression.png)

- Синий: исходный временной ряд
- Зеленый: подобранная линия
- Красный: две подобранные линии

> [!NOTE]
> Функция точно обнаружила точку перехода (изменения уровня).

### <a name="seasonality-detection"></a>Обнаружение сезонности

Многие метрики следуют сезонным (периодическим) закономерностям. Пользовательский трафик облачных служб обычно подчиняется ежедневным и еженедельным закономерностям с максимальными значениями в середине рабочего дня и минимальными — ночью и в выходные. Датчики Интернета вещей выполняют измерения с периодическими интервалами. Физические измерения, например температура, давление или влажность, также могут иметь сезонный характер.

В следующем примере применяется обнаружение сезонности к трафику веб-службы за один месяц (с 2-часовыми ячейками):

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Сезонность временного ряда](media/time-series-analysis/time-series-seasonality.png)

- Функция [series_periods_detect()](kusto/query/series-periods-detectfunction.md) позволяет автоматически обнаружить периоды во временных рядах. 
- Функция [series_periods_validate()](kusto/query/series-periods-validatefunction.md) полезна, если мы знаем, что метрика должна иметь определенные отличительные периоды, и хотим убедиться, что они существуют.

> [!NOTE]
> Если определенных отличительных периодов нет, это аномалия.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

| periods | scores (баллы) | days |
| --- | --- | --- |
| 84 | 0,820622786055595 | 7 |
| 12 | 0,764601405803502 | 1 |

Функция обнаруживает ежедневную и еженедельную сезонность. Ежедневный показатель меньше еженедельного, так как выходные дни отличаются от будних.

### <a name="element-wise-functions"></a>Функции с учетом элементов

Над временным рядом можно выполнять арифметические и логические операции. С помощью функции [series_subtract()](kusto/query/series-subtractfunction.md) мы можем вычислить остаточный временной ряд, то есть разность между исходной необработанной метрикой и сглаженной метрикой, и поискать аномалии в остаточном сигнале:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Операции над временным рядом](media/time-series-analysis/time-series-operations.png)

- Синий: исходный временной ряд
- Красный: сглаженный временной ряд
- Зеленый: остаточный временной ряд

## <a name="time-series-workflow-at-scale"></a>Масштабирование рабочего процесса с временными рядами

В приведенном ниже примере показано, как описанные функции могут выполняться в масштабе тысяч временных рядов за секунды для обнаружения аномалий. Чтобы просмотреть несколько примеров записей данных телеметрии для метрики количества операций чтения службы баз данных, выполните следующий запрос:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

| timestamp | Loc | anonOp | DB | DataRead |
| --- | --- | --- | --- | --- |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
| 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

И простая статистика:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

| num | min\_t | max\_t |
| --- | --- | --- |
| 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Построение временного ряда с 1-часовыми ячейками метрики операций чтения (всего четыре дня * 24 часа = 96 баллов) приводит к обычному колебанию закономерности:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Масштабирование временного ряда](media/time-series-analysis/time-series-at-scale.png)

Указанное выше поведение может ввести в заблуждение, так как один обычный временной ряд агрегируется из тысяч различных экземпляров, в которых возможны аномальные закономерности. Поэтому мы создаем по временному ряду на каждый экземпляр. Экземпляр определяется параметрами Loc (расположение), anonOp (операция) и DB (конкретный компьютер).

Сколько временных рядов мы можем создать?

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

| Счетчик |
| --- |
| 18 339 |

Теперь мы создадим набор из 18 339 временных рядов метрики количества операций чтения. Добавим предложение `by` в оператор make-series, применим линейную регрессию, а также выберем два верхних временных ряда, в которых возникла наиболее значительная тенденция уменьшения:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Первые два временных ряда](media/time-series-analysis/time-series-top-2.png)

Отобразим экземпляры:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

| Loc | Оператор | DB | slope |
| --- | --- | --- | --- |
| Loc 15 | 37 | 1151 | -102743.910227889 |
| Loc 13 | 37 | 1249 | -86303.2334644601 |

Менее чем за две минуты служба ADX проанализировала около 20 000 временных рядов и обнаружила два ряда с отклонениями, в которых число операций чтения неожиданно уменьшилось.

Эти расширенные возможности в сочетании с высокой производительностью ADX предоставляют уникальное и эффективное решение для анализа временных рядов.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте об [обнаружении аномалий во временных рядах и их прогнозировании](anomaly-detection.md) в Azure Data Explorer.
* Узнайте о [возможностях машинного обучения](machine-learning-clustering.md) в Azure Data Explorer.