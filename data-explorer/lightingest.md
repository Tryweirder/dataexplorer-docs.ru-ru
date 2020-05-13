---
title: Самое освещение — это программа командной строки для приема в Azure обозреватель данных.
description: Узнайте о самом освещении, программе командной строки для нерегламентированного приема данных в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8d4eeb47abb8eac2b042b64e65b55dac7e91d6c9
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374058"
---
# <a name="install-and-use-lightingest"></a>Установка и использование LightIngest

Самое освещение — это служебная программа командной строки для нерегламентированного приема данных в Azure обозреватель данных.
Программа может извлекать исходные данные из локальной папки или из контейнера хранилища BLOB-объектов Azure.

## <a name="prerequisites"></a>Предварительные требования

* Самое освещение. Скачайте его как часть [пакета Microsoft. Azure. Kusto. Tools NuGet.](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    ![Загрузка самого освещения](media/lightingest/lightingest-download-area.png)

* WinRAR — Скачайте его из [www.Win-RAR.com/download.HTML](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Установка самого освещения

1. Перейдите к расположению на компьютере, где вы загрузили освещение. 
1. С помощью WinRAR извлеките каталог *Tools* на компьютер.

## <a name="run-lightingest"></a>Запустить самое освещение

1. Перейдите к извлеченному каталогу *Tools* на компьютере.
1. Удалите существующие сведения о расположении из строки расположения.
    
      ![Удаление сведений о расположении](media/lightingest/lightingest-location-bar.png)

1. Введите `cmd` и нажмите клавишу **Ввод**.
1. В командной строке введите, `LightIngest.exe` за которым следует соответствующий аргумент командной строки.

    > [!Tip]
    > Чтобы получить список поддерживаемых аргументов командной строки, введите `LightIngest.exe /help` .
    >
    >![Справка по командной строке](media/lightingest/lightingest-cmd-line-help.png)

1. Введите, `ingest-` за которым следует строка подключения к кластеру Azure обозреватель данных, который будет управлять приемом.
    Заключите строку подключения в двойные кавычки и следуйте [спецификации строк подключения Kusto](kusto/api/connection-strings/kusto.md).

    Пример:
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Рекомендуемый метод заключается в том, чтобы самое освещение работало с конечной точкой приема в `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` . Таким образом служба обозреватель данных Azure может управлять нагрузкой приема, а также легко восстанавливать временные ошибки. Тем не менее можно также настроить освещение для работы непосредственно с конечной точкой подсистемы ( `https://{yourClusterNameAndRegion}.kusto.windows.net` ).

> [!Note]
> Если вы напрямую принимаете конечную точку ядра, вам не нужно включать `ingest-` , но не будет функции DM для защиты подсистемы и повышения частоты успешных приемов приема.

* Для оптимальной производительности приема важно, чтобы самым ясно было знание размера необработанных данных, и, что самое важное, оценивает размер несжатых локальных файлов. Однако самое освещение может оказаться неспособной правильно оценить необработанный размер сжатых больших двоичных объектов без их первоначальной загрузки. Поэтому при приеме сжатых больших двоичных объектов задайте `rawSizeBytes` для свойства метаданных большого двоичного объекта значение несжатого размера данных в байтах.

## <a name="general-command-line-arguments"></a>Общие аргументы командной строки

|Имя аргумента         |короткое имя;   |Тип    |Обязательный |Описание                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |строка  |Обязательный |[Строка подключения обозреватель данных Azure](kusto/api/connection-strings/kusto.md) , указывающая конечную точку Kusto, которая будет обрабатывать прием. Следует заключать в двойные кавычки |
|база данных             |-DB          |string  |Необязательно  |Имя целевой базы данных Azure обозреватель данных |
|-Table                |             |string  |Обязательный |Имя целевой таблицы Azure обозреватель данных |
|-sourcePath           |-source      |string  |Обязательный |Путь к исходным файлам или корневой URI контейнера больших двоичных объектов. Если данные находятся в больших двоичных объектах, они должны содержать ключ учетной записи хранения или SAS. Рекомендуется заключать в двойные кавычки |
|— префикс               |             |string  |Необязательно  |Если исходные данные для приема находятся в хранилище BLOB-объектов, этот префикс будет совместно использоваться всеми большими двоичными объектами, за исключением имени контейнера. <br>Например, если данные находятся в `MyContainer/Dir1/Dir2` , префикс должен иметь значение `Dir1/Dir2` . Рекомендуется заключать в двойные кавычки |
|-Pattern              |             |string  |Необязательно  |Шаблон, по которому выбираются исходные файлы и большие двоичные объекты. Поддерживает подстановочные знаки. Например, `"*.csv"`. Рекомендуется заключать в двойные кавычки |
|-Зиппаттерн           |             |string  |Необязательно  |Регулярное выражение, используемое при выборе файлов в ZIP-архиве для приема.<br>Все остальные файлы в архиве будут игнорироваться. Например, `"*.csv"` . Рекомендуется заключить его в двойные кавычки |
|-Format               |-f           |string  |Необязательно  |Формат исходных данных. Должен быть одним из [поддерживаемых форматов](ingestion-supported-formats.md) |
|-Инжестионмаппингпас |-Маппингпас |string  |Необязательно  |Путь к файлу сопоставления столбцов приема (обязателен для форматов JSON и Avro). См. [сопоставление данных](kusto/management/mappings.md) |
|-Инжестионмаппингреф  |-Маппингреф  |string  |Необязательно  |Имя предварительно созданного сопоставления столбца приема (обязательно для форматов JSON и Avro). См. [сопоставление данных](kusto/management/mappings.md) |
|-Креатионтимепаттерн  |             |string  |Необязательно  |Если задано, используется для извлечения свойства CreationTime из пути к файлу или большому двоичному объекту. См. раздел [Использование аргумента креатионтимепаттерн](#using-creationtimepattern-argument) |
|-Игнорефирстров       |-Игнорефирст |bool    |Необязательно  |Если задано, то первая запись каждого файла или большого двоичного объекта игнорируется (например, если исходные данные имеют заголовки). |
|-Tag                  |             |string  |Необязательно  |[Теги](kusto/management/extents-overview.md#extent-tagging) , связываемые с полученными данными. Разрешено несколько вхождений |
|-Донтваит             |             |bool    |Необязательно  |Если задано значение "true", не ждет завершения приема. Полезно при приеме больших объемов файлов или больших двоичных объектов |

### <a name="using-creationtimepattern-argument"></a>Использование аргумента Креатионтимепаттерн

`-creationTimePattern`Аргумент извлекает свойство CreationTime из пути к файлу или большому двоичному объекту. Шаблону не нужно отражать весь путь к элементу, просто раздел, в котором указана отметка времени, которую необходимо использовать.

Значения аргумента должны включать:
* Постоянный тест непосредственно перед меткой времени, заключенный в одинарные кавычки
* Формат метки времени в стандартной [нотации .NET DateTime](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Постоянный текст сразу после метки времени. Например, если имена больших двоичных объектов заканчиваются на `historicalvalues19840101.parquet` (отметка времени имеет четыре цифры для года, две цифры месяца и две цифры дня месяца), то соответствующее значение `-creationTimePattern` аргумента будет следующим:

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Аргументы командной строки для расширенных сценариев

|Имя аргумента         |короткое имя;   |Тип    |Обязательный |Описание                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-сжатие          |-CR          |double  |Необязательно  |Указание коэффициента сжатия. Полезно при приеме сжатых файлов и больших двоичных объектов, чтобы помочь Azure обозреватель данных оценить размер необработанных данных. Вычисляется как исходный размер, деленный на сжатый размер |
|-Limit                |-l           |Целое число |Необязательно  |Если задано, ограничение приема до первых N файлов |
|-Листонли             |-List        |bool    |Необязательно  |Если задано, отображаются только те элементы, которые были выбраны для приема| 
|-Инжесттимеаут        |             |Целое число |Необязательно  |Время ожидания для выполнения всех операций приема (в минутах). По умолчанию — `60`.|
|-Форцесинк            |             |bool    |Необязательно  |Если задано, инициирует синхронное получение. По умолчанию — `false`. |
|-Датабатчсизе        |             |Целое число |Необязательно  |Устанавливает общий предельный размер (МБ, без сжатия) каждой операции приема |
|-Филесинбатч         |             |Целое число |Необязательно  |Задает ограничение числа файлов и BLOB-объектов для каждой операции приема |
|-ДевтраЦинг           |-Trace       |string  |Необязательно  |Если задано, журналы диагностики записываются в локальный каталог (по умолчанию `RollingLogs` в текущем каталоге или могут быть изменены путем установки значения переключателя). |

## <a name="blob-metadata-properties"></a>Свойства метаданных BLOB-объекта
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

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

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
## <a name="changelog"></a>Журнал изменений
|Version        |Изменения                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Добавлен аргумент `-zipPattern` для .</li><li>Добавлен аргумент `-listOnly` для .</li><li>Сводка по аргументам отображается перед началом запуска</li><li>CreationTime считывается из свойств метаданных BLOB-объектов или из BLOB-объекта или имени файла в соответствии с `-creationTimePattern` аргументом</li></ul>|
