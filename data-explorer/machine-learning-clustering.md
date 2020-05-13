---
title: Возможности машинного обучения в Azure обозреватель данных
description: Используйте кластеризацию машинного обучения для анализа основных причин в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: abef0650485ac1feb53d43f42559c5a7fdfb75c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374037"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Возможности машинного обучения в Azure обозреватель данных

Обозреватель данных Azure, платформа для анализа больших данных, используется для мониторинга работоспособности служб, QoS или неисправности устройств для аномального поведения с помощью встроенных функций [обнаружения аномалий и прогнозирования](anomaly-detection.md) . После обнаружения аномального шаблона выполняется анализ основных причин (RCA) для устранения или устранения аномалий.

Процесс диагностики сложен и длительен и выполняется экспертами по предметной области. Этот процесс включает выборку и присоединение дополнительных данных из разных источников для одного и того же периода времени, Поиск изменений в распределении значений по нескольким измерениям, построение диаграммы дополнительных переменных и другие методы на основе знаний домена и интуиция. Так как эти сценарии диагностики распространены в Azure обозреватель данных, доступны подключаемые модули машинного обучения, позволяющие упростить фазу диагностики и сократить продолжительность RCA.

В обозреватель данных Azure есть три подключаемых модуля Машинное обучение: [`autocluster`](kusto/query/autoclusterplugin.md) , [`basket`](kusto/query/basketplugin.md) и [`diffpatterns`](kusto/query/diffpatternsplugin.md) . Все подключаемые модули реализуют алгоритмы кластеризации. `autocluster` `basket` Кластер подключаемых модулей и объединяет в кластере один набор записей, а в `diffpatterns` подключаемом модуле — различия между двумя наборами записей.

## <a name="clustering-a-single-record-set"></a>Кластеризация одного набора записей

Типичный сценарий включает набор данных, выбранный в соответствии с конкретными критериями, такими как временное окно, которое ведет к аномальному поведению, чтение устройств высокой температуры, команды длительной длительности и Топ пользователей по расходам. Нам хотелось бы легко и быстро найти в данных Общие шаблоны (сегменты). Шаблоны — это подмножество набора данных, записи которых имеют одни и те же значения в нескольких измерениях (столбцы категорий). Следующий запрос создает и отображает временные ряды исключений служб за неделю в 10-минутных ячейках:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Исключения службы диаграмму](media/machine-learning-clustering/service-exceptions-timechart.png)

Число исключений службы сопоставляется с общим трафиком службы. Можно ясно увидеть ежедневный шаблон для рабочих дней с понедельника по пятницу, при этом Счетчик исключений службы вырастет в середину дня, а счетчики — в течение ночного времени. Плоские неструктурированные счетчики видны в выходных. Пики исключений можно обнаружить с помощью [обнаружения аномалий временных рядов](anomaly-detection.md#time-series-anomaly-detection) в Azure обозреватель данных.

Второй пик данных происходит во вторник вечера. Следующий запрос используется для дальнейшей диагностики этого пика. Используйте запрос, чтобы перерисовать диаграмму вокруг пикового разрешения (восемь часов в одной минутной ячейке), чтобы проверить, является ли это резкой пик, и просмотреть его границы.

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Сосредоточьтесь на пиках диаграмму](media/machine-learning-clustering/focus-spike-timechart.png)

Мы видим 2-е минутный пик с 15:00 до 15:02. В следующем запросе подсчитайте исключения в этом 2-минутном окне:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Счетчик |
|---------|
|972    |

В следующем запросе пример 20 исключений из 972:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Регион | скалеунит | DeploymentId                     | Трассировки | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | нкус   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | нкус   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Использование автокластера () для кластеризации одного набора записей

Несмотря на то, что существует меньше тысяч исключений, трудно найти общие сегменты, так как в каждом столбце имеется несколько значений. Вы можете использовать [`autocluster()`](kusto/query/autoclusterplugin.md) подключаемый модуль для мгновенного извлечения небольшого списка общих сегментов и поиска интересных кластеров в течение двух минут, как показано в следующем запросе:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| Идентификатор сегмента | Счетчик | Процент | Регион | скалеунит | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | еау | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | SCUs | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | нкус | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | SCUs | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | веу | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Из результатов, приведенных выше, можно увидеть, что наиболее главный сегмент содержит 65,74% от общего числа записей исключений и использует четыре измерения. Следующий сегмент гораздо менее распространен, содержит только 9,67% записей и использует три измерения. Другие сегменты еще менее распространены. 

В автокластере используется собственный алгоритм для интеллектуального анализа нескольких измерений и извлечения интересных сегментов. «Интересное» означает, что каждый сегмент имеет значительный охват как набора записей, так и набора функций. Сегменты также отличаются друг от друга, что означает, что каждая из них существенно отличается от других. Один или несколько из этих сегментов могут быть актуальны для процесса RCA. Чтобы избежать рассмотрения и оценки сегмента, автокластер извлекает только небольшой список сегментов.

### <a name="use-basket-for-single-record-set-clustering"></a>Использование корзины () для кластеризации одиночных наборов записей

Вы также можете использовать [`basket()`](kusto/query/basketplugin.md) подключаемый модуль, как показано в следующем запросе:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| Идентификатор сегмента | Счетчик | Процент | Регион | скалеунит | DeploymentId | Трассировки | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | еау | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | еау | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | еау | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | еау | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | SCUs | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | нкус | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | SCUs | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | SCUs |  |  |  |  |
| 9 | 55 | 5.65843621399177 | веу | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Корзина реализует алгоритм Apriori для интеллектуального анализа элементов и извлекает все сегменты, покрытие которых превышает пороговое значение (по умолчанию — 5%). Вы видите, что другие сегменты были извлечены с похожими (например, сегментами 0, 1 или 2, 3).

Оба подключаемых модуля являются мощными и простыми в использовании, но их существенное ограничение заключается в том, что они кластерируют один набор записей неконтролируемым образом (без меток). Поэтому неясно, характеризуются ли извлеченные шаблоны выбранным набором записей (аномальные записи) или глобальным набором записей.

## <a name="clustering-the-difference-between-two-records-sets"></a>Кластеризация разницы между двумя наборами записей

[`diffpatterns()`](kusto/query/diffpatternsplugin.md)Подключаемый модуль ограничивает ограничение `autocluster` и `basket` . `Diffpatterns`принимает два набора записей и извлекает из них основные сегменты, которые отличаются. Один набор обычно содержит нетипичный набор записей, который анализируется (один проанализирован в `autocluster` и `basket` ). Другой набор содержит набор записей Reference (базовый). 

В приведенном ниже запросе мы используем `diffpatterns` для поиска интересных кластеров в течение двух минут, которые отличаются от кластеров в базовом плане. Определение окна "базовые показатели" определяется как восемь минут до 15:00 (после начала пик). Также необходимо расширить двоичный столбец (AB), указав, относится ли конкретная запись к базовому плану или к аномальному набору. `Diffpatterns`реализует контролируемый алгоритм обучения, в котором две метки классов были созданы аномальными и небазовыми флагами (AB).

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| Идентификатор сегмента | Число A | Число B | Процент A | Процент B | перцентдиффаб | Регион | скалеунит | DeploymentId | Трассировки |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65,74 | 1.7 | 64,04 | еау | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17,18 | 44,16 | 26,97 | SCUs |  |  |  |
| 2 | 92 | 356 | 9,47 | 28,9 | 19,43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27,27 | 18,01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8,44 | 25,81 | 17,38 | нкус | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5,66 | 20,45 | 14,8 | веу | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5,86 | 16,56 | 10,69 |  |  |  |  |

Самым главным сегментом является тот же сегмент, который был извлечен в `autocluster` , а его покрытие в течение двух минут — также 65,74%. Но его покрытие в окне "базовые показатели за восемь минут" составляет всего 1,7%. Разница составляет 64,04%. Похоже, эта разница связана с аномальными пиками. Это допущение можно проверить, разделив исходную диаграмму на записи, относящиеся к этому проблемному сегменту, и другие сегменты, как показано в следующем запросе:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Идет проверка сегмента "диффпаттерн" диаграмму](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Эта диаграмма позволяет нам увидеть, что пик вторника (Вторник) был из-за исключений из этого сегмента, обнаруженных с помощью `diffpatterns` подключаемого модуля.

## <a name="summary"></a>Сводка

Подключаемые модули Машинное обучение Azure обозреватель данных полезны во многих сценариях. `autocluster`И реализуют неконтролируемый `basket` алгоритм обучения и просты в использовании. `Diffpatterns`реализует контролируемый алгоритм обучения и, хотя и более сложный, он предоставляет более широкие возможности для извлечения сегментов различий для RCA.

Эти подключаемые модули используются в интерактивном режиме в нерегламентированных сценариях и в автоматизированных службах мониторинга практически в реальном времени. В обозреватель данных Azure за обнаружение аномалий временных рядов следует процесс диагностики, который оптимизирован для удовлетворения необходимых стандартов производительности.
