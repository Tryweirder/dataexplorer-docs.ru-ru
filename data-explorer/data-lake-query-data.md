---
title: Данные о запросах в Azure Data Lake с помощью Azure Data Explorer
description: Узнайте, как заставить данные в Azure Data Lake с помощью Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: aa1cdb647190c804c5dc18d1575a6030f166a845
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498582"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Данные о запросах в Azure Data Lake с помощью Azure Data Explorer

Azure Data Lake Storage — это высокомасштабируемое и экономное решение для анализа больших данных. Он сочетает в себе мощь высокопроизводительной файловой системы с массовым масштабом и экономикой, чтобы помочь вам ускорить ваше время, чтобы понять. Data Lake Storage Gen2 расширяет возможности хранения Azure Blob Storage и оптимизируется для аналитических нагрузок.
 
Azure Data Explorer интегрируется с Azure Blob Storage и Azure Data Lake Storage (Gen1 и Gen2), обеспечивая быстрый, кэшированный и индексированный доступ к данным в озере. Данные в озере можно анализировать и заготавлив без предварительного вхвнутри в Azure Data Explorer. Вы также можете задать запрос через проглоченные и uningested данных родного озера одновременно.  

> [!TIP]
> Наилучшая производительность запроса требует приема данных в Azure Data Explorer. Возможность запроса внешних данных без предварительного приема должна использоваться только для исторических данных или данных, которые редко запрашиваются. [Оптимизируйте производительность запроса в озере](#optimize-your-query-performance) для достижения наилучших результатов.
 

## <a name="create-an-external-table"></a>Создание внешней таблицы

 > [!NOTE]
 > В настоящее время поддерживаемыми учетными записями хранения являются Azure Blob Storage или Azure Data Lake Storage (Gen1 и Gen2).

1. Используйте `.create external table` команду для создания внешней таблицы в Azure Data Explorer. Дополнительные внешние команды `.show`таблицы, такие как , `.drop`и `.alter` задокументированы в внешних команд [таблицы.](kusto/management/externaltables.md)

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
    > * Ожидается повышение производительности с более детальной перегородкой. Например, запросы по внешним таблицам с ежедневными разделами будут иметь лучшую производительность, чем запросы с ежемесячными разделенными таблицами.
    > * При определении внешней таблицы с разделами структура хранилища должна быть идентичной.
Например, если таблица определена разделом DateTime в формате yyyy/MM/dd (по умолчанию), путь файла хранения URI должен быть *контейнерным1/yyyy/MM/dd/all_exported_blobs.* 
    > * Если внешняя таблица разделена столбцом времени даты, всегда включайте в запрос фильтр времени для `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` закрытого диапазона (например, запрос `ArchivedProducts | where Timestamp > ago(1h)` - должен работать лучше, чем этот (открытый диапазон) один - ). 
    > * Все [поддерживаемые форматы приема](ingest-data-overview.md#supported-data-formats) могут быть запрошены с помощью внешних таблиц.

1. Внешняя таблица видна в левом стеле веб-мина

    ![внешняя таблица в веб-ui](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Создание внешней таблицы с форматом json

Можно создать внешнюю таблицу с форматом json. Для получения дополнительной [информации см.](kusto/management/externaltables.md)

1. Используйте `.create external table` команду для создания таблицы под названием *ExternalTableJson:*

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
 
1. Формат Json требует второго шага создания отображения для столбцов, как показано ниже. В следующем запросе создайте определенное отображение json под названием *mappingName:*

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Разрешения на внешние таблицы
 
* Пользователь базы данных может создать внешнюю таблицу. Создатель таблицы автоматически становится администратором таблицы.
* Администратор кластера, базы данных или таблицы может отсеить существующую таблицу.
* Любой пользователь или читатель базы данных может загнать внешнюю таблицу.
 
## <a name="query-an-external-table"></a>Запрос внешней таблицы
 
Чтобы заставить заранее заставить таблицу заставить его использовать функцию `external_table()` и уведомить имя таблицы в качестве аргумента функции. Остальная часть запроса является стандартным языком запроса Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense в настоящее время не поддерживается на внешних настольных запросах.

### <a name="query-an-external-table-with-json-format"></a>Запрос внешней таблицы с форматом json

Чтобы заставить внешнюю таблицу с `external_table()` форматом json, используйте функцию и укажите как имя таблицы, так и имя отображения в качестве аргументов функции. В приведенном ниже запросе, если *отображениеНе* не указано, будет использовано отображение, которое вы создали ранее.

```kusto
external_table('ExternalTableJson', 'mappingName')
```

## <a name="query-external-and-ingested-data-together"></a>Запрос внешних и попадает данных вместе

Вы можете заразиться как внешними таблицами, так и таблицами данных в одном запросе. Вы [`join`](kusto/query/joinoperator.md) [`union`](kusto/query/unionoperator.md) или внешняя таблица с дополнительными данными из Azure Data Explorer, серверов S'L или других источников. Используйте [`let( ) statement`](kusto/query/letstatement.md) для присвоения значения для внешней таблицы.

В приведенном ниже примере *продукты* — это таблица данных, и *ArchivedProducts* — внешняя таблица, содержащая данные в Хранилище озера Лазурных Данных Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Запрос *TaxiRides* внешняя таблица в справочном кластере

Набор выборочных данных *TaxiRides* содержит данные такси в Нью-йорке от [Комиссии по такси и лимузину.](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

### <a name="create-external-table-taxirides"></a>Создание внешней таблицы *TaxiRides* 

> [!NOTE]
> В этом разделе показан запрос, используемый для создания внешней таблицы *TaxiRides* в кластере *справки.* Поскольку эта таблица уже создана, вы можете пропустить этот раздел и выполнить [данные внешних таблиц *TaxiRides.* ](#query-taxirides-external-table-data) 

1. Для создания внешней таблицы *TaxiRides* в справочном кластере был использован следующий запрос. 

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
1. Полученная таблица была *создана* в справочном кластере:

    ![TaxiRides внешний стол](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Запрос *TaxiRides* внешние данные таблицы 

Восватки для [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) запроса наружной таблицы *TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Запрос *TaxiRides* внешняя таблица без раздела

[Запустите этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) на внешней таблице *TaxiRides,* чтобы изобразить аттракционы для каждого дня недели, по всему набору данных. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Этот запрос показывает самый загруженный день недели. Поскольку данные не разделены, для возврата результатов (до нескольких минут) может потребоваться много времени.

![визуализация неразделимых запросов](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Запрос TaxiRides внешняя таблица с разделом 

[Запустите этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) на внешней таблице *TaxiRides* с указанием типов такси (желтый или зеленый), используемых в январе 2017 года. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Этот запрос использует раздел, который оптимизирует время запроса и производительность. Запрос фильтрует на разделенном столбце (pickup_datetime) и возвращаетрезультаты через несколько секунд.

![визуализация разделенного запроса](media/data-lake-query-data/taxirides-with-partition.png)
  
Вы можете написать дополнительные запросы для запуска на внешней таблице *TaxiRides* и узнать больше о данных. 

## <a name="optimize-your-query-performance"></a>Оптимизация производительности запроса

Оптимизируйте производительность запроса в озере, используя следующие рекомендации по запросу внешних данных. 
 
### <a name="data-format"></a>Формат данных
 
Используйте формат columnar для аналитических запросов, так как:
* Читать можно только столбцы, относящиеся к запросу. 
* Методы кодирования столбцов могут значительно уменьшить размер данных.  
Azure Data Explorer поддерживает форматы столбцов Паркета и ORC. Формат паркета предлагается за счет оптимизации реализации. 
 
### <a name="azure-region"></a>Регион Azure 
 
Установить, что внешние данные находятся в том же регионе Azure, что и кластер Azure Data Explorer. Это сокращает время извлечения затрат и данных.
 
### <a name="file-size"></a>Размер файла
 
Оптимальный размер файла составляет сотни Мб (до 1 Гб) за файл. Избегайте многих небольших файлов, требующих ненужных накладных расходов, таких как более медленный процесс перечисления файлов и ограниченное использование формата columnar. Обратите внимание, что количество файлов должно быть больше, чем количество ядер процессора в кластере Azure Data Explorer. 
 
### <a name="compression"></a>Сжатие
 
Используйте сжатие, чтобы уменьшить объем данных, извлекаемых из удаленного хранилища. Для формата Паркет используйте внутренний механизм сжатия паркета, который сжимает группы столбцов отдельно, что позволяет читать их отдельно. Для проверки использования механизма сжатия убедитесь, что файлы<filename>названы следующим образом:<filename>".gz.parquet" или<filename>".snappy.parquet", в отличие от ".parquet.gz"). 
 
### <a name="partitioning"></a>Секционирование
 
Организуйте свои данные с помощью разделов "папки", которые позволяют запросу пропускать неуместные пути. При планировании раздела учитывайте размер файла и общие фильтры в запросах, такие как метка времени или идентификатор клиента.
 
### <a name="vm-size"></a>Размер виртуальной машины
 
Выберите VM SKUs с большим количеством ядер и более высокой пропускной памятью (память менее важна). Для получения дополнительной информации [см. Выберите правильный VM SKU для кластера Azure Data Explorer.](manage-cluster-choose-sku.md)

## <a name="next-steps"></a>Следующие шаги

Запрос данных в Azure Data Lake с помощью Azure Data Explorer. Научитесь [писать запросы](write-queries.md) и получать дополнительные сведения из ваших данных.
