---
title: Используйте самое освещение для приема данных в Azure обозреватель данных.
description: Узнайте о самом освещении, программе командной строки для нерегламентированного приема данных в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/28/2020
ms.openlocfilehash: a56f5ea3ad17e8ef7c428d927861af9eaa0e9935
ms.sourcegitcommit: de81b57b6c09b6b7442665e5c2932710231f0773
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87264766"
---
# <a name="use-lightingest-to-ingest-data-to-azure-data-explorer"></a>Использование самого освещения для приема данных в Azure обозреватель данных
 
Самое освещение — это служебная программа командной строки для нерегламентированного приема данных в Azure обозреватель данных. Программа может извлекать исходные данные из локальной папки или из контейнера хранилища BLOB-объектов Azure.
Самое большое значение наиболее удобно, если требуется получить большой объем данных, так как для длительности приема не существует ограничения по времени. Это также полезно, если вы хотите позднее запрашивать записи в соответствии с моментом их создания, а не время их приема.

## <a name="prerequisites"></a>Предварительные требования

* Самое освещение. Скачайте его как часть [пакета Microsoft. Azure. Kusto. Tools NuGet.](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    :::image type="content" source="media/lightingest/lightingest-download-area.png" alt-text="Загрузка самого освещения":::

* WinRAR — Скачайте его из [www.win-rar.com/download.html](http://www.win-rar.com/download.html) .

## <a name="install-lightingest"></a>Установка самого освещения

1. Перейдите к расположению на компьютере, где вы загрузили освещение.
1. С помощью WinRAR извлеките каталог *Tools* на компьютер.

## <a name="run-lightingest"></a>Запустить самое освещение

1. Перейдите к извлеченному каталогу *Tools* на компьютере.
1. Удалите существующие сведения о расположении из строки расположения.
    
    :::image type="content" source="kusto/tools/images/KustoTools-Lightingest/lightingest-locationbar.png" alt-text="Удаление существующих сведений о расположении для освещения":::

1. Введите `cmd` и нажмите клавишу **ВВОД**.
1. В командной строке введите, `LightIngest.exe` за которым следует соответствующий аргумент командной строки.

    > [!Tip]
    > Чтобы получить список поддерживаемых аргументов командной строки, введите `LightIngest.exe /help` .
    >
    > :::image type="content" source="media/lightingest/lightingest-cmd-line-help.png" alt-text="Справка по командной строке для самого освещения":::

1. Введите, `ingest-` за которым следует строка подключения к кластеру Azure обозреватель данных, который будет управлять приемом.
    Заключите строку подключения в двойные кавычки и следуйте [спецификации строк подключения Kusto](kusto/api/connection-strings/kusto.md).

    Пример:

    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="recommendations"></a>Рекомендации

* Рекомендуемый метод заключается в том, чтобы самое освещение работало с конечной точкой приема в `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` . Таким образом служба обозреватель данных Azure может управлять нагрузкой приема, а также легко восстанавливать временные ошибки. Тем не менее можно также настроить освещение для работы непосредственно с конечной точкой подсистемы ( `https://{yourClusterNameAndRegion}.kusto.windows.net` ).

   > [!NOTE]
   > Если вы напрямую принимаете конечную точку ядра, не нужно включать `ingest-` . Однако функция DM не будет использоваться для защиты подсистемы и повышения скорости приема.

* Для оптимальной производительности приема размер необработанных данных необходим, поэтому освещение может оценить размер несжатых локальных файлов. Однако самое освещение может оказаться неспособной правильно оценить необработанный размер сжатых больших двоичных объектов без их первоначальной загрузки. Поэтому при приеме сжатых больших двоичных объектов задайте `rawSizeBytes` для свойства метаданных большого двоичного объекта значение несжатого размера данных в байтах.

## <a name="command-line-arguments"></a>аргументов командной строки;

|Имя аргумента            |Тип     |Описание       |Обязательный или необязательный
|------------------------------|--------|----------|-----------------------------|
|                               |строка   |[Строка подключения обозреватель данных Azure](kusto/api/connection-strings/kusto.md) , указывающая конечную точку Kusto, которая будет обрабатывать прием. Следует заключать в двойные кавычки | Обязательный
|-Database,-DB          |строка  |Имя целевой базы данных Azure обозреватель данных | Необязательно  |
|-Table                  |строка  |Имя целевой таблицы Azure обозреватель данных | Обязательный |
|-sourcePath,-Source      |строка  |Путь к исходным файлам или корневой URI контейнера больших двоичных объектов. Если данные находятся в больших двоичных объектах, они должны содержать ключ учетной записи хранения или SAS. Рекомендуется заключать в двойные кавычки |Обязательный |
|— префикс                  |строка  |Если исходные данные для приема находятся в хранилище BLOB-объектов, этот префикс будет совместно использоваться всеми большими двоичными объектами, за исключением имени контейнера. <br>Например, если данные находятся в `MyContainer/Dir1/Dir2` , префикс должен иметь значение `Dir1/Dir2` . Рекомендуется заключать в двойные кавычки | Необязательно  |
|-Pattern        |строка  |Шаблон, по которому выбираются исходные файлы и большие двоичные объекты. Поддерживает подстановочные знаки. Например, `"*.csv"`. Рекомендуется заключать в двойные кавычки | Необязательно  |
|-Зиппаттерн     |строка  |Регулярное выражение, используемое при выборе файлов в ZIP-архиве для приема.<br>Все остальные файлы в архиве будут игнорироваться. Например, `"*.csv"`. Рекомендуется заключить его в двойные кавычки | Необязательно  |
|-Format,-f           |строка  | Формат исходных данных. Должен быть одним из [поддерживаемых форматов](ingestion-supported-formats.md) | Необязательно  |
|-Инжестионмаппингпас,-Маппингпас |строка  |Путь к локальному файлу для сопоставления столбцов приема. Обязателен для форматов JSON и Avro. См. [сопоставление данных](kusto/management/mappings.md) | Необязательно  |
|-Инжестионмаппингреф,-Маппингреф  |строка  |Имя сопоставления столбца приема, созданного ранее для таблицы. Обязателен для форматов JSON и Avro. См. [сопоставление данных](kusto/management/mappings.md) | Необязательно  |
|-Креатионтимепаттерн      |строка  |Если задано, используется для извлечения свойства CreationTime из пути к файлу или большому двоичному объекту. Узнайте [, как принимать данные с `CreationTime` помощью](#how-to-ingest-data-using-creationtime) |Необязательно  |
|-Игнорефирстров,-Игнорефирст |bool    |Если задано, то первая запись каждого файла или большого двоичного объекта игнорируется (например, если исходные данные имеют заголовки). | Необязательно  |
|-Tag            |строка   |[Теги](kusto/management/extents-overview.md#extent-tagging) , связываемые с полученными данными. Разрешено несколько вхождений | Необязательно  |
|-Донтваит           |bool     |Если задано значение "true", не ждет завершения приема. Полезно при приеме больших объемов файлов или больших двоичных объектов |Необязательно  |
|-compression,-CR          |double |Указание коэффициента сжатия. Полезно при приеме сжатых файлов и больших двоичных объектов, чтобы помочь Azure обозреватель данных оценить размер необработанных данных. Вычисляется как исходный размер, деленный на сжатый размер |Необязательно  |
|-Limit,-l           |Целое число   |Если задано, ограничение приема до первых N файлов |Необязательно  |
|-Листонли,-List        |bool    |Если задано, отображаются только те элементы, которые были выбраны для приема| Необязательно  |
|-Инжесттимеаут   |Целое число  |Время ожидания для выполнения всех операций приема (в минутах). По умолчанию — `60`.| Необязательно  |
|-Форцесинк        |bool  |Если задано, инициирует синхронное получение. По умолчанию — `false`. |Необязательно  |
|-Датабатчсизе        |Целое число  |Устанавливает общий предельный размер (МБ, без сжатия) каждой операции приема |Необязательно  |
|-Филесинбатч            |Целое число |Задает ограничение числа файлов и BLOB-объектов для каждой операции приема |Необязательно  |
|-ДевтраЦинг,-Trace       |строка    |Если задано, журналы диагностики записываются в локальный каталог (по умолчанию `RollingLogs` в текущем каталоге или могут быть изменены путем установки значения переключателя). | Необязательно  |

## <a name="azure-blob-specific-capabilities"></a>Возможности, связанные с BLOB-объектами Azure

При использовании с большими двоичными объектами Azure для расширения процесса приема будут использоваться определенные свойства метаданных большого двоичного объекта.

|Свойство Metadata                            | Использование                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Если задано, будет интерпретироваться как размер несжатых данных                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Интерпретируется как метка времени в формате UTC. Если задано, будет использоваться для переопределения времени создания в Kusto. Полезно для сценариев заполнения |

## <a name="usage-examples"></a>Примеры использования

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Ingestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="how-to-ingest-data-using-creationtime"></a>Прием данных с помощью CreationTime

При загрузке исторических данных из существующей системы в обозреватель данных Azure все записи получают одинаковую дату приема. Чтобы включить секционирование данных по времени создания, а не по времени приема, можно использовать `-creationTimePattern` аргумент. `-creationTimePattern`Аргумент извлекает `CreationTime` свойство из пути к файлу или большому двоичному объекту. Шаблону не нужно отражать весь путь к элементу, просто раздел, в котором указана отметка времени, которую необходимо использовать.

Значения аргумента должны включать:
* Постоянный текст непосредственно перед форматом метки времени, заключенный в одинарные кавычки (префикс)
* Формат метки времени в стандартной [нотации .NET DateTime](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Постоянный текст сразу после метки времени (суффикс).

**Примеры** 

* Имя большого двоичного объекта, содержащего дату и время: `historicalvalues19840101.parquet` (отметка времени содержит четыре цифры для года, две цифры месяца и две цифры для дня месяца). 
    
    Значение `-creationTimePattern` аргумента является частью имени файла: *"' хисторикалвалуес'ииииммдд '. Parquet '"*

    ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Для URI большого двоичного объекта, который ссылается на иерархическую структуру папок, например `https://storageaccount/container/folder/2002/12/01/blobname.extension` , 

    Значение `-creationTimePattern` аргумента является частью структуры папок: *"' Folder/' гггг/mm/dd '/блоб '"*

   ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'folder/'yyyy/MM/dd'/blob'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Прием больших двоичных объектов с помощью ключа учетной записи хранения или маркера SAS

* Прием 10 больших двоичных объектов в указанной учетной записи хранения `ACCOUNT` , в папке в `DIR` контейнере `CONT` и в соответствии с шаблоном`*.csv.gz`
* Назначением является база данных `DB` , таблица `TABLE` , а сопоставление приема `MAPPING` создано в месте назначения
* Средство будет ожидать завершения операций приема
* Обратите внимание на различные параметры для указания целевой базы данных и ключа учетной записи хранения или маркера SAS.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Прием всех больших двоичных объектов в контейнере, не включая строки заголовка

* Прием всех BLOB-объектов в указанной учетной записи хранения `ACCOUNT` , в папке `DIR1/DIR2` , в контейнере `CONT` и сопоставление с шаблоном`*.csv.gz`
* Назначением является база данных `DB` , таблица `TABLE` , а сопоставление приема `MAPPING` создано в месте назначения
* Исходные BLOB-объекты содержат строку заголовка, поэтому средство указывает на удаление первой записи каждого большого двоичного объекта.
* Средство будет публиковать данные для приема и не будет ожидать завершения операций приема

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Прием всех JSON-файлов из пути

* Принимать все файлы по пути `PATH` , соответствующие шаблону`*.json`
* Назначением является база данных `DB` , таблица `TABLE` , а сопоставление приема определено в локальном файле`MAPPING_FILE_PATH`
* Средство будет публиковать данные для приема и не будет ожидать завершения операций приема

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Прием файлов и запись файлов трассировки диагностики

* Принимать все файлы по пути `PATH` , соответствующие шаблону`*.json`
* Назначением является база данных `DB` , таблица `TABLE` , а сопоставление приема определено в локальном файле`MAPPING_FILE_PATH`
* Средство будет публиковать данные для приема и не будет ожидать завершения операций приема
* Файлы трассировки диагностики будут записываться локально в папку`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
