---
title: Прием больших двоичных объектов Azure в Azure Data Explorer
description: Из этой статьи вы узнаете, как отправить данные учетной записи хранения в Azure обозреватель данных с помощью подписки на службу "Сетка событий".
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: cad16cf68b5b923c4ffef36370adb6506255dafd
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "86014120"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Прием больших двоичных объектов в службу Azure Data Explorer через подписку на уведомления службы "Сетка событий Azure"

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-grid-resource-manager.md)

Azure Data Explorer — это быстрая и масштабируемая служба для изучения данных журналов и телеметрии. Она позволяет непрерывно принимать (загружать) данные из больших двоичных объектов, записанных в контейнеры больших двоичных объектов.

Из этой статьи вы узнаете, как получать большие двоичные объекты из учетной записи хранения в Azure обозреватель данных с помощью подключения к данным в сетке событий. Вы создадите подключение к данным в сетке событий, которое задает подписку на службу " [Сетка событий Azure](/azure/event-grid/overview) ". Подписка на службу "Сетка событий" направляет события из вашей учетной записи хранения в Azure обозреватель данных через концентратор событий Azure. Затем вы увидите пример потока данных в пределах всей системы.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
* [Кластер и база данных](create-cluster-database-portal.md).
* [Учетная запись хранения](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

## <a name="create-a-target-table-in-azure-data-explorer"></a>Создание целевой таблицы в обозревателе данных Azure

Создайте таблицу в Azure Data Explorer, куда Центры событий будут отправлять данные. Таблица создается в кластере и базе данных, подготовленных в разделе предварительных требований.

1. На портале Azure, в своем кластере, выберите **Запрос**.

    :::image type="content" source="media/ingest-data-event-grid/query-explorer-link.png" alt-text="Ссылка на обозреватель запросов":::    

1. Скопируйте следующую команду в окно и выберите **Выполнить**, чтобы создать таблицу (TestTable), которая будет принимать входящие данные.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    :::image type="content" source="media/ingest-data-event-grid/run-create-table.png" alt-text="Выполнить команду создания таблицы":::

1. Скопируйте следующую команду в окно и выберите **Выполнить** для сопоставления входящих данных JSON с именами столбцов и типами данных таблицы (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Создание подключения к данным службы "Сетка событий Azure" в Azure Data Explorer

Теперь подключите учетную запись хранения к Azure обозреватель данных, чтобы данные, передаваемые в хранилище, переходили в поток в тестовую таблицу. 

1. В созданном кластере выберите **databases тестдатабасе (базы данных**)  >  **TestDatabase**.

    :::image type="content" source="media/ingest-data-event-grid/select-test-database.png" alt-text="Выбор тестовой базы данных":::

1. Выберите **приема данных**  >  **Добавить подключение к данным**.

    :::image type="content" source="media/ingest-data-event-grid/data-ingestion-create.png" alt-text="Добавление подключения к данным для приема данных":::

1. Выберите тип подключения: **хранилище BLOB-объектов**.

1. Заполните форму, указав следующую информацию.

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-basics.png" alt-text="Заполнение формы сетки событий с помощью основных сведений о соединении":::

     Источник данных:

    |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
    |---|---|---|
    | Имя подключения к данным | *test-grid-connection* | Имя создаваемого подключения в Azure Data Explorer.|
    | Подписка учетной записи хранения | идентификатор подписки; | Идентификатор подписки, в которой находится ваша учетная запись хранения.|
    | Учетная запись хранения | *gridteststorage1* | Имя созданной ранее учетной записи хранения.|
    | Создание ресурсов | *Automatic* (Автоматический) | Укажите, хотите ли вы, чтобы Azure обозреватель данных самостоятельно создать подписку на службу "Сетка событий", пространство имен концентратора событий и концентратор событий. Подробное описание создания подписки службы "Сетка событий" вручную можно найти в ссылках в разделе [Создание подписки на сетку событий в учетной записи хранения](../data-explorer/kusto/management/data-ingestion/eventgrid.md#create-an-event-grid-subscription-in-your-storage-account) .|

1. Выберите **Параметры фильтра** , если хотите отвести трассировку по конкретным темам. Настройте фильтры для получения уведомлений следующим образом:
    * Поле **prefix** — это префикс *литерала* субъекта. Как применяется шаблон *StartsWith*, он может охватывать несколько контейнеров, папок или больших двоичных объектов. Подстановочные знаки не допускаются.
        * Чтобы определить фильтр для контейнера больших двоичных объектов, *необходимо* установить поле следующим образом: *`/blobServices/default/containers/[container prefix]`* .
        * Чтобы определить фильтр для префикса большого двоичного объекта (или папки в Azure Data Lake Gen2), поле *должно* быть установлено следующим образом: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * Поле **суффикса** — это *литеральный* суффикс большого двоичного объекта. Подстановочные знаки не допускаются.
    * Поле **с учетом регистра** указывает, учитывается ли регистр в фильтрах префиксов и суффиксов
    * Дополнительные сведения о фильтрации событий см. в разделе [события хранилища BLOB-объектов](/azure/storage/blobs/storage-blob-event-overview#filtering-events).
    
    :::image type="content" source="media/ingest-data-event-grid/filter-settings.png" alt-text="Сетка событий параметров фильтра":::    

1. Нажмите кнопку **Далее: приема свойств**.

1. Заполните форму следующими сведениями и нажмите кнопку **Далее: Проверка и создание**. Имена таблиц и сопоставлений учитывают регистр:

   :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-ingest-properties.png" alt-text="Проверка и создание свойств приема таблиц и сопоставлений":::

    Свойства приема:

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Таблица | *TestTable* | Таблица, созданная в базе данных **TestDatabase**. |
    | Формат данных | *JSON* | Поддерживаются форматы Avro, CSV, JSON, многострочные JSON, ORC, PARQUET, ПСВ, СКСВ, СОХСВ, TSV, TXT и ТСВЕ. Поддерживаемые параметры сжатия: ZIP и GZip |
    | Сопоставление | *TestMapping* | Сопоставление, созданное в таблице **TestDatabase**, которое сопоставляет входящие данные JSON с именами столбцов и типами данных **TestTable**.|

1. Проверьте созданные для вас ресурсы и нажмите кнопку **создать**.

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Проверка и создание подключения к данным для сетки событий":::

1. Подождите, пока развертывание завершится. В случае сбоя развертывания можно выбрать **сведения об операции** рядом с этапом сбоя, чтобы получить дополнительные сведения о причинах сбоя. Можно также выбрать повторное **развертывание** , чтобы повторить попытку развертывания ресурсов.

    :::image type="content" source="media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Развертывание ресурсов сетки событий":::

## <a name="generate-sample-data"></a>Создание примера данных

Теперь, когда Azure обозреватель данных и учетная запись хранения подключены, можно создать демонстрационные данные и передать их в контейнер хранилища.

Мы создадим небольшой скрипт оболочки, который выполняет несколько базовых команд Azure CLI для взаимодействия с ресурсами службы хранилища Azure. Этот сценарий выполняет следующие действия: 
1. Создает контейнер в учетной записи хранения.
1. Отправляет существующий файл (в виде большого двоичного объекта) в этот контейнер.
1. Перечисляет большие двоичные объекты в контейнере. 

Чтобы выполнить скрипт непосредственно на портале, можно использовать [Azure Cloud Shell](/azure/cloud-shell/overview).

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
> Для достижения наилучшей производительности приема данных размер сжатых больших двоичных *объектов,* отправленных для приема, должен быть передан. Поскольку уведомления в сетке событий содержат только основные сведения, сведения о размере должны быть явно переданы. Сведения о размере без сжатия можно предоставить, задав `rawSizeBytes` для свойства в метаданных большого двоичного объекта размер *несжатых* данных в байтах.

### <a name="ingestion-properties"></a>Свойства приема

[Свойства приема](ingestion-properties.md) большого двоичного объекта можно указать с помощью метаданных большого двоичного объекта.

Эти свойства можно задать.

|**Свойство** | **Описание свойства**|
|---|---|
| `rawSizeBytes` | Размер необработанных (несжатых) данных. Для Avro/ORC/Parquet, который имеет размер до применения сжатия, характерного для формата.|
| `kustoTable` |  Имя существующей целевой таблицы. Переопределяет `Table` набор в `Data Connection` колонке. |
| `kustoDataFormat` |  Формат данных. Переопределяет `Data format` набор в `Data Connection` колонке. |
| `kustoIngestionMappingReference` |  Имя существующего сопоставления приема, которое будет использоваться. Переопределяет `Column mapping` набор в `Data Connection` колонке.|
| `kustoIgnoreFirstRecord` | Если задано значение `true` , Kusto игнорирует первую строку большого двоичного объекта. Для пропуска заголовков используйте данные в табличном формате (CSV, TSV или аналогичные). |
| `kustoExtentTags` | Строка, представляющая [теги](kusto/management/extents-overview.md#extent-tagging) , которые будут присоединены к результирующему экстенту. |
| `kustoCreationTime` |  Переопределяет [$IngestionTime](kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) для большого двоичного объекта в формате строки ISO 8601. Используется для заполнения. |

> [!NOTE]
> Azure обозреватель данных не удалит прием BLOB-объектов после приема.
> Храните большие двоичные объекты в течение трех – пять дней.
> Использование [жизненного цикла хранилища BLOB-объектов Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением больших двоичных объектов. 

## <a name="review-the-data-flow"></a>Просмотр потока данных

> [!NOTE]
> В Azure Data Explorer настроена политика агрегирования (пакетной обработки) для приема данных, которая предназначена для оптимизации приема.
По умолчанию политика настроена на прием данных с интервалом в 5 минут.
При необходимости вы сможете изменить политику позже. В этой статье вы можете заждаться задержки в течение нескольких минут.

1. На портале Azure в разделе сетки событий во время работы приложения появится пик активности.

    :::image type="content" source="media/ingest-data-event-grid/event-grid-graph.png" alt-text="Граф активности для сетки событий":::

1. Чтобы проверить, сколько сообщений поступило в базу данных к этому моменту, выполните следующий запрос в тестовой базе данных.

    ```kusto
    TestTable
    | count
    ```

1. Чтобы увидеть содержимое сообщений, выполните следующий запрос в тестовой базе данных.

    ```kusto
    TestTable
    ```

    Результирующий набор должен выглядеть, как на следующем рисунке:

    :::image type="content" source="media/ingest-data-event-grid/table-result.png" alt-text="Результирующий набор сообщений для сетки событий":::

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать сетку событий еще раз, очистите подписку на сетку событий, пространство имен концентратора событий и концентратор событий, которые были созданы ранее, чтобы избежать затрат на оплату.

1. В портал Azure перейдите в меню слева и выберите **все ресурсы**.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Выбрать все ресурсы для очистки сетки событий":::    

1. Найдите пространство имен концентратора событий и выберите **Удалить** , чтобы удалить его:

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-find-eventhub-namespace-delete.png" alt-text="Очистка пространства имен концентратора событий":::

1. В форме удаление ресурсов подтвердите удаление, чтобы удалить пространство имен концентратора событий и ресурсы концентратора событий.

1. Войдите в свою учетную запись хранения. В меню слева выберите **события**:

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Выберите события для очистки в сетке событий":::

1. Под диаграммой выберите подписку на сетку событий и щелкните **Удалить** , чтобы удалить ее:

    :::image type="content" source="media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Удалить подписку на сетку событий":::

1. Чтобы удалить подключение к данным в сетке событий, перейдите в кластер Azure обозреватель данных. В меню слева выберите **базы данных**.

1. Выберите базу данных **тестдатабасе**:

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="Выбор базы данных для очистки ресурсов":::

1. В меню слева выберите прием **данных**:

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="Выбор приема данных для очистки ресурсов":::

1. Выберите подключение к данным *Проверка-сетка — подключение* , а затем щелкните **Удалить** , чтобы удалить его.

## <a name="next-steps"></a>Дальнейшие действия

* [Запрос данных в обозреватель данных Azure](web-query-data.md)
