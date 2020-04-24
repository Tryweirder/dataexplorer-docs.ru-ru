---
title: Прием больших двоичных объектов Azure в Azure Data Explorer
description: В этой статье вы узнаете, как отправлять данные учетной записи хранилища в Azure Data Explorer с помощью подписки Event Grid.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ba7305eca81ed405f2a577c4a994133509429564
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500519"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Прием больших двоичных объектов в службу Azure Data Explorer через подписку на уведомления службы "Сетка событий Azure"

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-grid-resource-manager.md)

Azure Data Explorer — это быстрая и масштабируемая служба для изучения данных журналов и телеметрии. Она позволяет непрерывно принимать (загружать) данные из больших двоичных объектов, записанных в контейнеры больших двоичных объектов. 

В этой статье вы узнаете, как настроить подписку [Azure Event Grid](/azure/event-grid/overview) и направить события в Azure Data Explorer через концентратор событий. Чтобы начать, необходимо иметь учетную запись хранения с подпиской на службу "Сетка событий Azure", которая отправляет уведомления в Центры событий Azure. Затем вы сможете создать подключение к данным службы "Сетка событий" и просмотреть поток данных в системе.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Создайте [бесплатную учетную запись Azure.](https://azure.microsoft.com/free/)
* [Кластер и база данных](create-cluster-database-portal.md).
* [Учетная запись хранилища](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Концентратор событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Создание подписки на службу "Сетка событий Azure" в учетной записи хранения

1. Найдите свою учетную запись хранения на портале Azure .
1. Выберите**Подписку событий**событий . **Events** > 

    ![Ссылка приложения запроса](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. В окне **Создание подписки на событие** на вкладке **Основные** введите следующие значения:

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Имя | *test-grid-connection* | Имя создаваемой сетки событий.|
    | Схема событий | *Схема сетки событий* | Схема, которая должна использоваться для сетки событий. |
    | Тип раздела | *Учетная запись хранения* | Тип раздела сетки событий. |
    | Ресурс раздела | *gridteststorage* | Имя учетной записи хранения. |
    | Подписка на все типы событий | *Ясно* | Позволяет указать, что получать уведомления обо всех событиях не нужно. |
    | Определенные типы событий | *Blob created* | Позволяет указать, о каких конкретных событиях нужно получать уведомления. |
    | Тип конечной точки | *Центры событий* | Тип конечной точки, куда будут отправляться события. |
    | Конечная точка | *test-hub* | Созданный концентратор событий. |
    | | |

1. Выберите вкладку **Фильтры,** если вы хотите отслеживать файлы из определенного контейнера. Настройте фильтры для получения уведомлений следующим образом:
    * Поле **Тема начинается с** является префиксом*литерала* контейнера больших двоичных объектов. Так как применен шаблон *startswith*, он может охватывать несколько контейнеров. Подстановочные знаки не допускаются.
     Значение *необходимо* задать следующим образом: *`/blobServices/default/containers/`*[префикс контейнера].
    * Поле **Тема заканчивается на** — это суффикс *литерала* большого двоичного объекта. Подстановочные знаки не допускаются.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Создание целевой таблицы в обозревателе данных Azure

Создайте таблицу в Azure Data Explorer, куда Центры событий будут отправлять данные. Таблица создается в кластере и базе данных, подготовленных в разделе предварительных требований.

1. На портале Azure, в своем кластере, выберите **Запрос**.

    ![Ссылка приложения запроса](media/ingest-data-event-grid/query-explorer-link.png)

1. Скопируйте следующую команду в окно и выберите **Выполнить**, чтобы создать таблицу (TestTable), которая будет принимать входящие данные.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Выполнение создания запроса](media/ingest-data-event-grid/run-create-table.png)

1. Скопируйте следующую команду в окно и выберите **Выполнить** для сопоставления входящих данных JSON с именами столбцов и типами данных таблицы (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Создание подключения к данным службы "Сетка событий Azure" в Azure Data Explorer

Теперь подключитесь к сетке событий из Azure Data Explorer, чтобы данные, впадающие в контейнер blob, перетекались в тестовую таблицу. 

1. Выберите **уведомления** на панели инструментов, чтобы убедиться в успешном развертывании концентратора событий.

1. В созданном кластере выберите **Database базы данных** > **TestDatabase.**

    ![Выбор тестовой базы данных](media/ingest-data-event-grid/select-test-database.png)

1. Выберите подключение **данных к данным** > Data**Add.**

    ![Прием данных](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Выберите тип соединения: **Blob Storage**.

1. Заполните форму, указав следующую информацию, и нажмите на кнопку **Создать**.

    ![Подключение концентратора событий](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Источник данных:

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Имя подключения к данным | *test-hub-connection* | Имя создаваемого подключения в Azure Data Explorer.|
    | Подписка учетной записи хранения | идентификатор подписки; | Идентификатор подписки, где находится учетная запись хранилища.|
    | Учетная запись хранения | *gridteststorage* | Имя созданной ранее учетной записи хранения.|
    | Сетка событий Azure | *test-grid-connection* | Имя созданной сетки событий. |
    | имя концентратора событий; | *test-hub* | Созданный концентратор событий. Это поле заполняется автоматически при выборе сетки событий. |
    | Группа потребителей | *test-group* | Группа потребителей, определенная в созданном концентраторе событий. |
    | | |

    Целевая таблица

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Таблица | *TestTable* | Таблица, созданная в базе данных **TestDatabase**. |
    | Формат данных | *JSON* | Поддерживаемые форматы: Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW и TXT. Поддерживаемые варианты сжатия: Цип и Гип |
    | Сопоставление столбцов | *TestMapping* | Сопоставление, созданное в таблице **TestDatabase**, которое сопоставляет входящие данные JSON с именами столбцов и типами данных **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Создание примера данных

Теперь, когда Azure Data Explorer и учетная запись хранения подключены, вы можете создать примеры данных и отправить их в хранилище BLOB-объектов.

Мы создадим небольшой скрипт оболочки, который выполняет несколько базовых команд Azure CLI для взаимодействия с ресурсами службы хранилища Azure. Этот скрипт создает новый контейнер в вашей учетной записи хранения, передает существующий файл (как большой двоичный объект) в этот контейнер и выводит список больших двоичных объектов в контейнере. Чтобы выполнить скрипт непосредственно на портале, можно использовать [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

Сохраните данные в файл и отправьте его с помощью этого скрипта:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Для достижения наилучшей производительности приема необходимо сообщить *несжатый* размер сжатых капли, представленные для приема. Поскольку уведомления Event Grid содержат только основные сведения, информация о размере должна быть явно передана. Информация о несжатых размерах `rawSizeBytes` может быть предоставлена путем установки свойства на метаданных капли с *несжатым* размером данных в байтах.

### <a name="ingestion-properties"></a>Свойства приема

Вы можете указать [свойства проглатывания](ingestion-properties.md) капли через метаданные капли.

Эти свойства могут быть установлены:

|**Свойство** | **Описание свойства**|
|---|---|
| `rawSizeBytes` | Размер необработанных (несжатых) данных. Для Avro/ORC/Parquet это размер до применения сжатия формата.|
| `kustoTable` |  Название существующей целевой таблицы. Переопределяет `Table` набор на `Data Connection` лезвии. |
| `kustoDataFormat` |  Формат данных. Переопределяет `Data format` набор на `Data Connection` лезвии. |
| `kustoIngestionMappingReference` |  Название существующего отображения приема, которое будет использоваться. Переопределяет `Column mapping` набор на `Data Connection` лезвии.|
| `kustoIgnoreFirstRecord` | Если `true`установлено, Кусто игнорирует первый ряд капли. Используйте данные табликового формата (CSV, TSV или аналогичный) для игнорирования заголовков. |
| `kustoExtentTags` | Строка, представляющая [теги,](kusto/management/extents-overview.md#extent-tagging) которые будут прикреплены в результирующей степени. |
| `kustoCreationTime` |  Оверлет$IngestionTime для капли, отформатированный как строка ISO 8601. [$IngestionTime](kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) Используйте для заправки. |

> [!NOTE]
> Azure Data Explorer не будет удалять проглатку капли.
> Сохранить капли для thrre до пяти дней.
> Используйте [жизненный цикл хранения Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением капли. 

## <a name="review-the-data-flow"></a>Просмотр потока данных

> [!NOTE]
> В Azure Data Explorer настроена политика агрегирования (пакетной обработки) для приема данных, которая предназначена для оптимизации приема.
По умолчанию политика настроена на прием данных с интервалом в 5 минут.
При необходимости вы сможете изменить политику позже. В этой статье можно ожидать задержки в несколько минут.

1. На портале Azure в разделе сетки событий во время работы приложения появится пик активности.

    ![График сетки событий](media/ingest-data-event-grid/event-grid-graph.png)

1. Чтобы проверить, сколько сообщений поступило в базу данных к этому моменту, выполните следующий запрос в тестовой базе данных.

    ```kusto
    TestTable
    | count
    ```

1. Чтобы увидеть содержимое сообщений, выполните следующий запрос в тестовой базе данных.

    ```kusto
    TestTable
    ```

    Результат должен быть похож на следующее.

    ![Набор результатов для сообщений](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если не планируется повторно использовать сетку событий, очистите **test-hub-rg**, чтобы избежать взимания оплаты.

1. На портале Azure выберите **Группа ресурсов** слева, а затем выберите созданную группу ресурсов.  

    Если левое меню свернуто, нажмите ![кнопку "Развернуть",](media/ingest-data-event-grid/expand.png) чтобы развернуть его.

   ![Выбор удаляемой группы ресурсов](media/ingest-data-event-grid/delete-resources-select.png)

1. В разделе **test-resource-group** выберите **Удалить группу ресурсов**.

1. В новом окне введите имя удаляемой группы ресурсов (*test-hub-rg*) и нажмите на кнопку **Удалить**.

## <a name="next-steps"></a>Следующие шаги

* [Данные запросов в Azure Data Explorer](web-query-data.md)
