---
title: LightIngest — это утилита командной строки для приема в Azure Data Explorer.
description: Узнайте о LightIngest, утилите командной строки для вхвнутри знания специальных данных в Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: e9918452aaeefc2c4f144c60e2e7bdb70ac87f2b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502417"
---
# <a name="install-and-use-lightingest"></a>Установка и использование LightIngest

LightIngest — это утилита командной строки для вхотливания специальных данных в Azure Data Explorer.
Утилита может извлекать исходные данные из локальной папки или из контейнера для хранения капли Azure.

## <a name="prerequisites"></a>Предварительные требования

* LightIngest - скачать его в рамках [пакета Microsoft.Azure.Kusto.Tools NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    ![Освещение скачать](media/lightingest/lightingest-download-area.png)

* WinRAR - скачать его из [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Установка Lightingest

1. Перейдите к местоположению на компьютере, где вы скачали LightIngest. 
1. Используя WinRAR, извлеките каталог *инструментов* на компьютер.

## <a name="run-lightingest"></a>Выполнить Lightingest

1. Перейдите к каталогу извлеченных *инструментов* на компьютере.
1. Удалите существующую информацию о местоположении из панели местоположения.
    
      ![Удаление информации о местоположении](media/lightingest/lightingest-location-bar.png)

1. Введите и нажмите `cmd` **Введите**.
1. В запросе команды `LightIngest.exe` введите, после чего будет актуальна соответствующая командная строка.

    > [!Tip]
    > Для списка поддерживаемых аргументов командной `LightIngest.exe /help`строки введите .
    >
    >![Справка по командной строке](media/lightingest/lightingest-cmd-line-help.png)

1. Введите `ingest-` строку подключения к кластеру Azure Data Explorer, который будет управлять проглатыванием.
    Привязайте строку соединения в двойные кавычки и следуйте [спецификации строк соединения Kusto.](kusto/api/connection-strings/kusto.md)

    Пример:
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Рекомендуемый метод для LightIngest для работы с `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`конечным точек приема на . Таким образом, служба Azure Data Explorer может управлять нагрузкой на проглатку, и вы можете легко восстановиться после временных ошибок. Тем не менее, вы также можете настроить LightIngest`https://{yourClusterNameAndRegion}.kusto.windows.net`работать непосредственно с конечным двигателем ().

> [!Note]
> Если вы глотаете непосредственно с конечной точкой `ingest-`двигателя, вам не нужно включать, но не будет функции DM для защиты двигателя и улучшения скорости приема.

* Для оптимальной производительности приема, важно для LightIngest знать размер необработанных данных и поэтому LightIngest будет оценивать несжатый размер локальных файлов. Тем не менее, LightIngest не сможет правильно оценить размер сжатых каплей без предварительной загрузки их. Поэтому при глотании сжатых капли `rawSizeBytes` установите свойство на метаданных капли на несжатый размер данных в байтах.

## <a name="general-command-line-arguments"></a>Общие аргументы командной строки

|Имя аргумента         |короткое имя;   |Тип    |Обязательный |Описание                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |строка  |Обязательный |[Строка подключения Azure Data Explorer](kusto/api/connection-strings/kusto.md) с указанием конечная точка Kusto, которая будет обрабатывать проглатываемый. Должны быть заключены в двойные котировки |
|-База данных             |-db          |строка  |Необязательно  |Целевое имя базы данных Azure Data Explorer |
|-таблица                |             |строка  |Обязательный |Целевое имя таблицы Исследователя данных Azure |
|-sourcePath           |-source      |строка  |Обязательный |Путь к исходным файлам или корневой URI контейнера с каплями. Если данные находятся в капли, должны содержать ключ учетной записи хранилища или SAS. Рекомендуется приложить в двойных котировках |
|-префикс               |             |строка  |Необязательно  |Когда исходные данные для проглования находятся на хранилище капли, эта приставка URL используется всеми каплями, за исключением имени контейнера. <br>Например, если данные `MyContainer/Dir1/Dir2`в, то приставка должна быть. `Dir1/Dir2` Рекомендуется прилагать двойные котировки |
|-шаблон              |             |строка  |Необязательно  |Шаблон, по которому выбираются исходные файлы/капли. Поддерживает подстановочные знаки. Например, `"*.csv"`. Рекомендуется приложить в двойных котировках |
|-zipPattern           |             |строка  |Необязательно  |Регулярное выражение для использования при выборе файлов в архиве ЗИП для глотания.<br>Все остальные файлы в архиве будут проигнорированы. Например, `"*.csv"`. Рекомендуется окружать его двойными котировками |
|-формат               |-f           |строка  |Необязательно  |Формат исходных данных. Должен быть одним из [поддерживаемых форматов](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) |
|-IngestionMappingPath |-отображениеПат |строка  |Необязательно  |Путь к файлу отображения столбцов (обязательный для форматов Json и Avro). Посмотреть [картографии данных](kusto/management/mappings.md) |
|-IngestionMappingRef  |-картографированиеРеф  |строка  |Необязательно  |Название предварительно созданного отображения столбца приема (обязательно для форматов Json и Avro). Посмотреть [картографии данных](kusto/management/mappings.md) |
|-созданиеTimePattern  |             |строка  |Необязательно  |При наборе используется для извлечения свойства CreationTime из файла или пути капли. Посмотреть [с помощью аргумента CreationTimePattern](#using-creationtimepattern-argument) |
|-игнорироватьПервыйРоу       |-игнорироватьПервый |bool    |Необязательно  |При установке первая запись каждого файла/капли игнорируется (например, если в исходных данных есть заголовки) |
|-тег                  |             |строка  |Необязательно  |[Теги](kusto/management/extents-overview.md#extent-tagging) для ассоциирования с просачиваемыми данными. Допускается несколько случаев |
|-DontWait             |             |bool    |Необязательно  |Если установлен на "истинное", не дожидаясь завершения приема. Полезно при глотая большое количество файлов / blobs |

### <a name="using-creationtimepattern-argument"></a>Использование аргумента CreationTimePattern

Аргумент `-creationTimePattern` извлекает свойство CreationTime из файла или пути капли. Шаблон не должен отражать весь путь элемента, только раздел, охватывающий метку времени, который вы хотите использовать.

Значения аргумента должны включать:
* Постоянный тест, непосредственно предшествующий метке времени, заключенный в одиночные кавычки
* Формат метки времени, в стандартной [нотации .NET DateTime](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Постоянный текст сразу же после метки времени. Например, если имена blob заканчиваются `historicalvalues19840101.parquet` (метка времени составляет четыре цифры за год, две цифры за месяц и `-creationTimePattern` две цифры за день месяца), соответствующее значение для аргумента:

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Аргументы командной строки для продвинутых сценариев

|Имя аргумента         |короткое имя;   |Тип    |Обязательный |Описание                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-Сжатие          |-cr          |double  |Необязательно  |Подсказка соотношения сжатия. Полезно при глотании сжатых файлов/капли, чтобы помочь Azure Data Explorer оценить размер необработанных данных. Рассчитано как исходный размер, разделенный на сжатый размер |
|-лимит                |-l           |Целое число |Необязательно  |Если установлено, ограничивает проглатывание первыми файлами N |
|-listТолько             |-список        |bool    |Необязательно  |Если набор, только отображает элементы, которые были бы выбраны для приема| 
|-ingestTimeout        |             |Целое число |Необязательно  |Тайм-аут в минутах для завершения всех операций глоток. По умолчанию — `60`.|
|-forceSync            |             |bool    |Необязательно  |Если набор, силы синхронного приема. По умолчанию — `false`. |
|-DataBatchРазмер        |             |Целое число |Необязательно  |Устанавливает общий предел размера (MB, несжатый) каждой операции по проглатываниям |
|-filesinbatch         |             |Целое число |Необязательно  |Устанавливает лимит количества файлов/блобов каждой операции по проглочению |
|-devTracing           |-трассировка       |строка  |Необязательно  |При установке диагностические журналы записываются в `RollingLogs` локальный каталог (по умолчанию, в текущем каталоге, или могут быть изменены, установив значение коммутатора) |

## <a name="blob-metadata-properties"></a>Свойства метаданных Blob
При использовании с azure blobs LightIngest будет использовать определенные свойства метаданных капли для увеличения процесса приема.

|Свойство метаданных                            | Использование                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Если набор, будет интерпретироваться как несжатый размер данных                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Интерпретируется как метка времени UTC. Если набор, будет использоваться для переопределения времени создания в Кусто. Полезно для сценариев заполнения |

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

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Проглование капли с помощью ключа учетной записи хранилища или маркера SAS

* Глоток 10 капли под `ACCOUNT`указанным `DIR`счетом `CONT`хранения, в папке , под контейнером , и соответствие шаблона`*.csv.gz`
* Направление — `DB`это `TABLE`база данных, таблица, и отображение `MAPPING` проглатывания создается на пункте назначения
* Инструмент будет ждать, пока операции по проглоту
* Обратите внимание на различные варианты указания целевой базы данных и ключа учетной записи хранилища против токена SAS

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

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Проглование всех капли в контейнере, не включая строки заголовка

* Ingest все капли под `ACCOUNT`указанным счетом хранения, в папке `DIR1/DIR2`, под контейнером `CONT`, и соответствие шаблона`*.csv.gz`
* Направление — `DB`это `TABLE`база данных, таблица, и отображение `MAPPING` проглатывания создается на пункте назначения
* Источник капли содержат заголовок линии, так что инструмент поручил отказаться от первой записи каждого капли
* Инструмент будет размещать данные для приема и не будет ждать, пока операции по проглатыванию для завершения

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

### <a name="ingesting-all-json-files-from-a-path"></a>Проем всех файлов JSON с пути

* Проем все файлы под траекторией, `PATH`соответствующий шаблону`*.json`
* Направление — `DB`это `TABLE`база данных, таблица, и отображение проглатывания определяется в локальном файле`MAPPING_FILE_PATH`
* Инструмент будет размещать данные для приема и не будет ждать, пока операции по проглатыванию для завершения

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Проем файлов и написание диагностических файлов трассировки

* Проем все файлы под траекторией, `PATH`соответствующий шаблону`*.json`
* Направление — `DB`это `TABLE`база данных, таблица, и отображение проглатывания определяется в локальном файле`MAPPING_FILE_PATH`
* Инструмент будет размещать данные для приема и не будет ждать, пока операции по проглатыванию для завершения
* Файлы отслеживания диагностики будут записаны локально под папкой`LOGS_PATH`

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
|Версия        |Изменения                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Добавлен аргумент `-zipPattern` для .</li><li>Добавлен аргумент `-listOnly` для .</li><li>Резюме аргументов отображается перед запуском</li><li>CreationTime читается по свойствам метаданных blob или от `-creationTimePattern` капли или имени файла, согласно аргументу</li></ul>|
