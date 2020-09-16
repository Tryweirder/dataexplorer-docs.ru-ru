---
title: Запрос данных в Azure Data Lake с помощью обозреватель данных Azure
description: Узнайте, как запрашивать данные в Azure Data Lake с помощью обозреватель данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 058a42cc21c6af9642d91231e6b1620315f94f55
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680694"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Запрос данных в Azure Data Lake с помощью обозреватель данных Azure

Azure Data Lake Storage — это высокомасштабируемое и экономичное решение Data Lake для анализа больших данных. Она сочетает мощь высокопроизводительной файловой системы с обширным масштабированием и экономией, чтобы помочь вам сократить время анализа. Data Lake Storage 2-го поколения расширяет возможности хранилища BLOB-объектов Azure и оптимизирован для рабочих нагрузок аналитики.
 
Azure обозреватель данных интегрируется с хранилищем BLOB-объектов Azure и Azure Data Lake Storage (GEN1 и Gen2), обеспечивая быстрый, кэшированный и индексированный доступ к данным, хранящимся во внешнем хранилище. Вы можете анализировать и запрашивать данные без предварительного приема в Azure обозреватель данных. Кроме того, можно запрашивать одновременно полученные и неполученные внешние данные.  

> [!TIP]
> Оптимальная производительность запросов требует приема данных в Azure обозреватель данных. Возможность запрашивать внешние данные без предварительного приема данных должна быть использована только для исторические данные или данные, которые редко запрашиваются. [Оптимизируйте производительность запросов внешних данных, чтобы](#optimize-your-query-performance) получить лучшие результаты.
 
## <a name="create-an-external-table"></a>Создание внешней таблицы

Предположим, у вас есть множество CSV-файлов, содержащих исторические сведения о продуктах, хранящихся в хранилище, и вы хотите выполнить быстрый анализ, чтобы найти пять наиболее популярных продуктов за прошлый год. В этом примере CSV-файлы выглядят следующим образом:

| Отметка времени | ProductId   | ProductDescription |
|-----------|-------------|--------------------|
| 2019-01-01 11:21:00 | TO6050 | 3.5 на гибком диске DS/HD |
| 2019-01-01 11:30:55 | YDX1   | Синтезатор DX1 Ямаха  |
| ...                 | ...    | ...                     |

Файлы хранятся в хранилище BLOB-объектов Azure в `mycompanystorage` контейнере с именем `archivedproducts` , секционированном по дате:

```
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00001-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00002-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00003-cd5fad16-a45e-4f8c-a2d0-5ea5de2f4e02.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ffc72d50-ff98-423c-913b-75482ba9ec86.csv.gz
...
```

Чтобы выполнить запрос ККЛ к этим CSV-файлам напрямую, используйте `.create external table` команду, чтобы определить внешнюю таблицу в обозреватель данных Azure. Дополнительные сведения о параметрах команды "внешняя таблица Create" см. в разделе [команды внешней таблицы](kusto/management/external-tables-azurestorage-azuredatalake.md).

```Kusto
.create external table ArchivedProducts(Timestamp:datetime, ProductId:string, ProductDescription:string)   
kind=blob            
partition by (Date:datetime = bin(Timestamp, 1d))   
dataformat=csv   
(   
  h@'https://mycompanystorage.blob.core.windows.net/archivedproducts;StorageSecretKey'
)    
```

Теперь внешняя таблица отображается в левой области веб-интерфейса:

:::image type="content" source="media/data-lake-query-data/external-tables-web-ui.png" alt-text="Внешняя таблица в веб-ИНТЕРФЕЙСе":::
 
### <a name="external-table-permissions"></a>Разрешения внешней таблицы
 
* Пользователь базы данных может создать внешнюю таблицу. Создатель таблицы автоматически станет администратором таблиц.
* Администратор кластера, базы данных или таблицы может изменять существующую таблицу.
* Любой пользователь или читатель базы данных может запрашивать внешнюю таблицу.

## <a name="querying-an-external-table"></a>Запрос к внешней таблице
 
После определения внешней таблицы `external_table()` можно использовать функцию для ссылки на нее. Остальная часть запроса — это стандартный язык запросов Kusto.

```Kusto
external_table("ArchivedProducts")   
| where Timestamp > ago(365d)   
| summarize Count=count() by ProductId,   
| top 5 by Count
```

## <a name="querying-external-and-ingested-data-together"></a>Совместное выполнение запросов внешних и полученных данных

В одном запросе можно запрашивать как внешние таблицы, так и полученные таблицы данных. Вы можете [`join`](kusto/query/joinoperator.md) или [`union`](kusto/query/unionoperator.md) внешнюю таблицу с дополнительными данными из обозреватель данных Azure, серверов SQL Server или других источников. Используйте, [`let( ) statement`](kusto/query/letstatement.md) чтобы присвоить сокращенное имя ссылке на внешнюю таблицу.

В приведенном ниже примере *Products* является принимающей таблицей данных, а *арчиведпродуктс* — внешней таблицей, которая была определена ранее:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);   
let T = Products; //T is an internal table   
T1 | join T on ProductId | take 10
```

## <a name="querying-hierarchical-data-formats"></a>Запрос к иерархическим форматам данных

Обозреватель данных Azure позволяет выполнять запросы к иерархическим форматам, таким как `JSON` ,, `Parquet` `Avro` и `ORC` . Чтобы сопоставить иерархическую схему данных со схемой внешней таблицы (если она отличается), используйте [команды сопоставления внешних таблиц](kusto/management/external-tables-azurestorage-azuredatalake.md#create-external-table-mapping). Например, если требуется запрашивать файлы журнала JSON в следующем формате:

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

Определение внешней таблицы выглядит следующим образом:

```kusto
.create external table ApiCalls(Timestamp: datetime, TenantId: guid, MethodName: string)
kind=blob
dataformat=multijson
( 
   h@'https://storageaccount.blob.core.windows.net/container1;StorageSecretKey'
)
```

Определите сопоставление JSON, которое сопоставляет поля данных с полями определения внешней таблицы:

```kusto
.create external table ApiCalls json mapping 'MyMapping' '[{"Column":"Timestamp","Properties":{"Path":"$.timestamp"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]'
```

При запросе к внешней таблице будет вызвано сопоставление, а соответствующие данные будут сопоставлены со столбцами внешней таблицы:

```kusto
external_table('ApiCalls') | take 10
```

Дополнительные сведения о синтаксисе сопоставления см. в разделе [сопоставления данных](kusto/management/mappings.md).

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Запрос внешней таблицы *таксиридес* в справочном кластере

Используйте тестовый кластер с именем *Help* , чтобы испытать различные возможности обозреватель данных Azure. *Справочный* кластер содержит определение внешней таблицы для [набора данных о такси городе Нью-Йорка](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) , содержащего миллиарды такси.

### <a name="create-external-table-taxirides"></a>Создание внешней таблицы *таксиридес* 

В этом разделе показан запрос, используемый для создания внешней таблицы *таксиридес* в кластере *справки* . Так как эта таблица уже создана, можно пропустить этот раздел и перейти непосредственно к [запросам данных внешней таблицы *таксиридес* ](#query-taxirides-external-table-data).

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
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

Вы можете найти созданную таблицу **таксиридес** , просмотрев левую панель веб-интерфейса:

:::image type="content" source="media/data-lake-query-data/taxirides-external-table.png" alt-text="Такси заменяет внешнюю таблицу":::

### <a name="query-taxirides-external-table-data"></a>Запрос данных внешней таблицы *таксиридес* 

Выполните вход на странице [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples). 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Запрос внешней таблицы *таксиридес* без секционирования

[Выполните этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) во внешней таблице *таксиридес* , чтобы отобразить значения для каждого дня недели во всем наборе данных. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Этот запрос показывает самый загруженный день недели. Так как данные не секционированы, запрос может занять до нескольких минут, чтобы получить результаты.

:::image type="content" source="media/data-lake-query-data/taxirides-no-partition.png" alt-text="обработать несекционированный запрос":::

#### <a name="query-taxirides-external-table-with-partitioning"></a>Запрос внешней таблицы Таксиридес с секционированием 

[Выполните этот запрос](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) во внешней таблице *таксиридес*  , чтобы отобразить типы CAB-файлов такси (желтые или зеленые), используемые в январе 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

В этом запросе используется секционирование, что позволяет оптимизировать время и производительность запросов. Фильтры запросов в секционированном столбце (pickup_datetime) и возвращают результаты через несколько секунд.

:::image type="content" source="media/data-lake-query-data/taxirides-with-partition.png" alt-text="Отобразить секционированный запрос":::
  
Можно написать дополнительные запросы для выполнения во внешней таблице *таксиридес* и получить дополнительные сведения о данных. 

## <a name="optimize-your-query-performance"></a>Оптимизация производительности запросов

Оптимизируйте производительность запросов в Lake, используя следующие рекомендации по запросам внешних данных. 
 
### <a name="data-format"></a>Формат данных
 
* Используйте формат столбцов для аналитических запросов по следующим причинам.
    * Только столбцы, относящиеся к запросу, могут быть считаны. 
    * Методы кодирования столбцов могут значительно снизить размер данных.  
* Azure обозреватель данных поддерживает форматы Parquet и ORC столбцов. Рекомендуется формат Parquet из-за оптимизированной реализации. 
 
### <a name="azure-region"></a>Регион Azure 
 
Убедитесь, что внешние данные находятся в том же регионе Azure, что и кластер Azure обозреватель данных. Эта настройка сокращает затраты и время выборки данных.
 
### <a name="file-size"></a>Размер файла
 
Оптимальный размер файла составляет сотни Мб (до 1 ГБ) на файл. Избегайте большого количества небольших файлов, требующих ненужных затрат, таких как медленный процесс перечисления файлов и ограниченное использование формата столбцов. Число файлов должно быть больше числа ядер ЦП в кластере Azure обозреватель данных. 
 
### <a name="compression"></a>Сжатие
 
Сжатие используется для уменьшения объема данных, извлекаемых из удаленного хранилища. Для формата Parquet Используйте механизм внутреннего сжатия Parquet, который сжимает группы столбцов отдельно, позволяя считывать их по отдельности. Чтобы проверить использование механизма сжатия, убедитесь, что имена файлов имеют следующий вид: * &lt; filename &gt; . gz. Parquet* или * &lt; filename &gt; . привязки. Parquet* , а не * &lt; filename &gt; . Parquet. gz*. 
 
### <a name="partitioning"></a>Секционирование
 
Упорядочите данные с помощью разделов "папка", которые позволяют запросу пропускать ненужные пути. При планировании секционирования учитывайте размер файла и общие фильтры в запросах, таких как метка времени или идентификатор клиента.
 
### <a name="vm-size"></a>Размер виртуальной машины
 
Выберите номера SKU виртуальных машин с большим количеством ядер и более высокой пропускной способностью сети (память менее важна). Дополнительные сведения см. [в статье Выбор подходящего номера SKU виртуальной машины для кластера Azure обозреватель данных](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Дальнейшие действия

* Запросите данные в Azure Data Lake с помощью обозреватель данных Azure. Научитесь [писать запросы](write-queries.md) и получать дополнительные аналитические сведения из данных.
