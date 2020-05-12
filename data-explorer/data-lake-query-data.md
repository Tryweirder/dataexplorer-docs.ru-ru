---
title: Запрос данных в Azure Data Lake с помощью обозреватель данных Azure
description: Узнайте, как запрашивать данные в Azure Data Lake с помощью обозреватель данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: b7ca4a41bb15e9fb8a4f55a9d674b1536dddd5ae
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226403"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Запрос данных в Azure Data Lake с помощью обозреватель данных Azure

Azure Data Lake Storage — это высокомасштабируемое и экономичное решение Data Lake для анализа больших данных. Она сочетает мощь высокопроизводительной файловой системы с крупномасштабным масштабированием и экономией для ускорения анализа. Data Lake Storage 2-го поколения расширяет возможности хранилища BLOB-объектов Azure и оптимизирован для рабочих нагрузок аналитики.
 
Azure обозреватель данных интегрируется с хранилищем BLOB-объектов Azure и Azure Data Lake Storage (GEN1 и Gen2), обеспечивая быстрый, кэшированный и индексированный доступ к данным в Lake. Вы можете анализировать и запрашивать данные в службе Lake без предварительного приема в Azure обозреватель данных. Кроме того, можно выполнять запросы одновременно из полученных и непринятых собственных данных Lake.  

> [!TIP]
> Оптимальная производительность запросов требует приема данных в Azure обозреватель данных. Возможность запрашивать внешние данные без предварительного приема данных должна быть использована только для исторические данные или данные, которые редко запрашиваются. [Оптимизируйте производительность запросов в Lake, чтобы](#optimize-your-query-performance) получить лучшие результаты.
 

## <a name="create-an-external-table"></a>Создание внешней таблицы

 > [!NOTE]
 > В настоящее время поддерживаются учетные записи хранения BLOB-объектов Azure или Azure Data Lake Storage (GEN1 и Gen2).

1. Используйте `.create external table` команду, чтобы создать внешнюю таблицу в обозреватель данных Azure. Дополнительные команды внешней таблицы, такие как `.show` , `.drop` и, `.alter` описаны в [командах внешней таблицы](kusto/management/externaltables.md).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * При более детализированном секционировании ожидается повышение производительности. Например, запросы к внешним таблицам с ежедневными секциями будут иметь лучшую производительность, чем запросы с месячными секционированными таблицами.
    > * При определении внешней таблицы с секциями структура хранения должна быть идентичной.
Например, если таблица определена с Секцией даты в формате гггг/мм/дд (по умолчанию), то путь к файлу хранилища URI должен быть *container1/гггг/мм/дд/all_exported_blobs*. 
    > * Если внешняя таблица секционирована по столбцу datetime, всегда включайте фильтр времени для закрытого диапазона в запросе (например, запрос `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` должен выполняться лучше, чем этот (открытый диапазон) `ArchivedProducts | where Timestamp > ago(1h)` . 
    > * Все [Поддерживаемые форматы приема](ingestion-supported-formats.md) можно запрашивать с помощью внешних таблиц.

1. Внешняя таблица отображается в левой области веб-интерфейса.

    ![Внешняя таблица в веб-ИНТЕРФЕЙСе](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Создание внешней таблицы в формате JSON

Можно создать внешнюю таблицу в формате JSON. Дополнительные сведения см. в разделе [команды внешней таблицы](kusto/management/externaltables.md) .

1. Используйте `.create external table` команду, чтобы создать таблицу с именем *екстерналтаблежсон*:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Формат JSON требует второго шага создания сопоставления со столбцами, как показано ниже. В следующем запросе создайте определенное сопоставление JSON с именем *mappingName*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Разрешения внешней таблицы
 
* Пользователь базы данных может создать внешнюю таблицу. Создатель таблицы автоматически станет администратором таблиц.
* Администратор кластера, базы данных или таблицы может изменять существующую таблицу.
* Любой пользователь или читатель базы данных может запрашивать внешнюю таблицу.
 
## <a name="query-an-external-table"></a>Запрос к внешней таблице
 
Чтобы запросить внешнюю таблицу, используйте `external_table()` функцию и укажите имя таблицы в качестве аргумента функции. Остальная часть запроса — это стандартный язык запросов Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Технология IntelliSense в настоящее время не поддерживается для запросов внешних таблиц.

### <a name="query-an-external-table-with-json-format"></a>Запрос к внешней таблице в формате JSON

Чтобы запросить внешнюю таблицу в формате JSON, используйте `external_table()` функцию и укажите имя таблицы и имя сопоставления в качестве аргументов функции. В приведенном ниже запросе, если *mappingName* не указан, будет использоваться ранее созданное сопоставление.

```kusto
external_table('ExternalTableJson', 'mappingName')
```

## <a name="query-external-and-ingested-data-together"></a>Совместный запрос внешних и полученных данных

В одном запросе можно запрашивать как внешние таблицы, так и полученные таблицы данных. Вы [`join`](kusto/query/joinoperator.md) или [`union`](kusto/query/unionoperator.md) внешняя таблица с дополнительными данными из обозреватель данных Azure, серверов SQL Server или других источников. Используйте, [`let( ) statement`](kusto/query/letstatement.md) чтобы присвоить сокращенное имя ссылке на внешнюю таблицу.

В приведенном ниже примере *Products* является принимающей таблицей данных, а *арчиведпродуктс* — внешней таблицей, содержащей данные в Azure Data Lake Storage 2-го поколения:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Запрос внешней таблицы *таксиридес* в справочном кластере

Образец набора данных *таксиридес* содержит сведения о городе Нью Йорк из [Нью такси и лимузинов комиссии](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Создание внешней таблицы *таксиридес* 

> [!NOTE]
> В этом разделе описывается запрос, используемый для создания внешней таблицы *таксиридес* в кластере *справки* . Так как эта таблица уже создана, можно пропустить этот раздел и выполнить [запрос к данным внешней таблицы *таксиридес* ](#query-taxirides-external-table-data). 

1. Следующий запрос использовался для создания внешней таблицы *таксиридес* в кластере справки. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. Результирующая таблица была создана в кластере *справки* :

    ![Внешняя таблица Таксиридес](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Запрос данных внешней таблицы *таксиридес* 

Войдите в, чтобы [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) выполнить запрос к внешней таблице *таксиридес* . 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Запрос внешней таблицы *таксиридес* без секционирования

[Выполните этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) во внешней таблице *таксиридес* , чтобы отобразить значения для каждого дня недели во всем наборе данных. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Этот запрос показывает самый загруженный день недели. Поскольку данные не секционированы, выполнение этого запроса может занять много времени (до нескольких минут).

![обработать несекционированный запрос](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Запрос внешней таблицы Таксиридес с секционированием 

[Выполните этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) во внешней таблице *таксиридес* , где показаны типы CAB-файлов такси (желтые или зеленые), используемые в январе 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

В этом запросе используется секционирование, что позволяет оптимизировать время и производительность запросов. Фильтры запросов в секционированном столбце (pickup_datetime) и возвращают результаты через несколько секунд.

![отобразить секционированный запрос](media/data-lake-query-data/taxirides-with-partition.png)
  
Можно написать дополнительные запросы для выполнения во внешней таблице *таксиридес* и получить дополнительные сведения о данных. 

## <a name="optimize-your-query-performance"></a>Оптимизация производительности запросов

Оптимизируйте производительность запросов в Lake, используя следующие рекомендации по запросам внешних данных. 
 
### <a name="data-format"></a>Формат данных
 
Используйте формат столбцов для аналитических запросов, начиная с:
* Только столбцы, относящиеся к запросу, могут быть считаны. 
* Методы кодирования столбцов могут значительно снизить размер данных.  
Azure обозреватель данных поддерживает форматы Parquet и ORC столбцов. Формат Parquet предлагается из-за оптимизированной реализации. 
 
### <a name="azure-region"></a>Регион Azure 
 
Выясните, что внешние данные находятся в том же регионе Azure, что и кластер Azure обозреватель данных. Это сокращает затраты и время выборки данных.
 
### <a name="file-size"></a>Размер файла
 
Оптимальный размер файла составляет сотни Мб (до 1 ГБ) на файл. Избегайте большого количества небольших файлов, требующих ненужных затрат, таких как медленный процесс перечисления файлов и ограниченное использование формата столбцов. Обратите внимание, что число файлов должно быть больше числа ядер ЦП в кластере Azure обозреватель данных. 
 
### <a name="compression"></a>Сжатие
 
Сжатие используется для уменьшения объема данных, извлекаемых из удаленного хранилища. Для формата Parquet Используйте механизм внутреннего сжатия Parquet, который сжимает группы столбцов отдельно, тем самым позволяя считывать их по отдельности. Чтобы проверить использование механизма сжатия, убедитесь, что имена файлов имеют следующий вид: " <filename> . gz. Parquet" или " <filename> . привязка. Parquet", а не " <filename> . Parquet. gz"). 
 
### <a name="partitioning"></a>Секционирование
 
Упорядочите данные с помощью разделов "Folder", которые позволяют запросу пропускать ненужные пути. При планировании секционирования учитывайте размер файла и общие фильтры в запросах, таких как метка времени или идентификатор клиента.
 
### <a name="vm-size"></a>Размер виртуальной машины
 
Выберите номера SKU виртуальных машин с большим количеством ядер и более высокой пропускной способностью сети (память менее важна). Дополнительные сведения см. [в статье Выбор подходящего номера SKU виртуальной машины для кластера Azure обозреватель данных](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Дальнейшие действия

Запросите данные в Azure Data Lake с помощью обозреватель данных Azure. Научитесь [писать запросы](write-queries.md) и получать дополнительные аналитические сведения из данных.
