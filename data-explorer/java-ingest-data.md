---
title: Прием данных с помощью пакета SDK для Java обозреватель данных Azure
description: Из этой статьи вы узнаете, как принимать (загружать) данные в Azure обозреватель данных с помощью пакета SDK для Java.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 45b0ce7b1716a4ed2ab7277b6c99a5d95f8ad5af
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906372"
---
# <a name="ingest-data-using-the-azure-data-explorer-java-sdk"></a>Прием данных с помощью пакета SDK для Azure обозреватель данных Java 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [GO](go-ingest-data.md)
> * [Java](java-ingest-data.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. [Клиентскую библиотеку Java](kusto/api/java/kusto-java-client-library.md) можно использовать для приема данных, выполнения команд управления и запроса данных в кластерах обозреватель данных Azure.

Из этой статьи вы узнаете, как принимать данные с помощью библиотеки Azure обозреватель данных Java. Во-первых, вы создадите таблицу и сопоставление данных в тестовом кластере. Затем вы помещаете в очередь прием из хранилища BLOB-объектов в кластер с помощью пакета SDK для Java и проверяете результаты.

## <a name="prerequisites"></a>Обязательные условия

* [Бесплатная учетная запись Azure](https://azure.microsoft.com/free/).
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* JDK версии 1,8 или более поздней.
* [Maven](https://maven.apache.org/download.cgi).
* [Кластер и база данных](create-cluster-database-portal.md).
* Создайте [регистрацию приложения и предоставьте ей разрешения на доступ к базе данных](provision-azure-ad-app.md). Сохраните идентификатор клиента и секрет клиента для последующего использования.

## <a name="review-the-code"></a>Просмотр кода

Этот раздел является необязательным. Ознакомьтесь с приведенными ниже фрагментами кода, чтобы узнать, как работает код. Чтобы пропустить этот раздел, перейдите на страницу [Запуск приложения](#run-the-application).

### <a name="authentication"></a>Аутентификация

Программа использует Azure Active Directory учетные данные для проверки подлинности с помощью Коннектионстрингбуилдер.

1. Создание `com.microsoft.azure.kusto.data.Client` для запросов и управления.

    ```java
    static Client getClient() throws Exception {
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(endpoint, clientID, clientSecret, tenantID);
        return ClientFactory.createClient(csb);
    }
    ```

1. Создание и использование `com.microsoft.azure.kusto.ingest.IngestClient` для отправки данных в очередь обозреватель данных Azure:

    ```java
    static IngestClient getIngestionClient() throws Exception {
        String ingestionEndpoint = "https://ingest-" + URI.create(endpoint).getHost();
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(ingestionEndpoint, clientID, clientSecret);
        return IngestClientFactory.createClient(csb);
    }
    ```

### <a name="management-commands"></a>Команды управления

[Команды управления](kusto/management/commands.md), такие как [`.drop`](kusto/management/drop-function.md) и [`.create`](kusto/management/create-function.md) , выполняются путем вызова `execute` для `com.microsoft.azure.kusto.data.Client` объекта.

Например, `StormEvents` Таблица создается следующим образом:

```java
static final String createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)";

static void createTable(String database) {
    try {
        getClient().execute(database, createTableCommand);
        System.out.println("Table created");
    } catch (Exception e) {
        System.out.println("Failed to create table: " + e.getMessage());
        return;
    }
    
}
```

### <a name="data-ingestion"></a>Прием данных

Прием очереди с помощью файла из существующего контейнера хранилища BLOB-объектов Azure. 
* Используйте `BlobSourceInfo` , чтобы указать путь к хранилищу BLOB-объектов.
* Используется `IngestionProperties` для определения таблицы, базы данных, имени сопоставления и типа данных. В следующем примере тип данных — `CSV` .

```java
    ...
    static final String blobPathFormat = "https://%s.blob.core.windows.net/%s/%s%s";
    static final String blobStorageAccountName = "kustosamplefiles";
    static final String blobStorageContainer = "samplefiles";
    static final String fileName = "StormEvents.csv";
    static final String blobStorageToken = "??st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    ....

    static void ingestFile(String database) throws InterruptedException {
        String blobPath = String.format(blobPathFormat, blobStorageAccountName, blobStorageContainer,
                fileName, blobStorageToken);
        BlobSourceInfo blobSourceInfo = new BlobSourceInfo(blobPath);

        IngestionProperties ingestionProperties = new IngestionProperties(database, tableName);
        ingestionProperties.setDataFormat(DATA_FORMAT.csv);
        ingestionProperties.setIngestionMapping(ingestionMappingRefName, IngestionMappingKind.Csv);
        ingestionProperties.setReportLevel(IngestionReportLevel.FailuresAndSuccesses);
        ingestionProperties.setReportMethod(IngestionReportMethod.QueueAndTable);
    ....
```

Процесс приема начинается в отдельном потоке, а `main` поток ожидает завершения потока приема. В этом процессе используется [каунтдовнлатч](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html). API приема ( `IngestClient#ingestFromBlob` ) не является асинхронным. `while`Цикл используется для опроса текущего состояния каждые 5 секунд и ожидает перехода состояния приема на `Pending` другое состояние. Конечным состоянием может быть `Succeeded` , `Failed` или `PartiallySucceeded` .

```java
        ....
        CountDownLatch ingestionLatch = new CountDownLatch(1);
        new Thread(new Runnable() {
            @Override
            public void run() {
                IngestionResult result = null;
                try {
                    result = getIngestionClient().ingestFromBlob(blobSourceInfo, ingestionProperties);
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
                try {
                    IngestionStatus status = result.getIngestionStatusCollection().get(0);
                    while (status.status == OperationStatus.Pending) {
                        Thread.sleep(5000);
                        status = result.getIngestionStatusCollection().get(0);
                    }
                    ingestionLatch.countDown();
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
            }
        }).start();
        ingestionLatch.await();
    }
```

> [!TIP]
> Существуют и другие методы для асинхронной обработки приема для различных приложений. Например, можно использовать [`CompletableFuture`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) для создания конвейера, определяющего действие после приема, например запрос таблицы или обработка исключений, переданных в `IngestionStatus` .

## <a name="run-the-application"></a>Выполнение приложения

### <a name="general"></a>Общие сведения

При запуске примера кода выполняются следующие действия.

   1. **DROP TABLE** : `StormEvents` таблица удалена (если она существует).
   1. **Создание таблицы** : `StormEvents` таблица создана.
   1. **Создание сопоставления** : `StormEvents_CSV_Mapping` сопоставление создано.
   1. Прием **файлов** : CSV-файл (в хранилище BLOB-объектов Azure) помещается в очередь для приема.

Ниже приведен пример кода `App.java` :

```java
public static void main(final String[] args) throws Exception {
    dropTable(database);
    createTable(database);
    createMapping(database);
    ingestFile(database);
}
```

> [!TIP]
> Чтобы попробовать различные сочетания операций, раскомментируйте соответствующие методы в `App.java` .

### <a name="run-the-application"></a>Выполнение приложения

1. Клонировать пример кода из GitHub:

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-java-sdk-ingest.git
    cd azure-data-explorer-java-sdk-ingest
    ```

1. Задайте сведения о субъекте-службе со следующими сведениями в качестве переменных среды, используемых программой:
    * Конечная точка кластера 
    * Имя базы данных 

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. Сборка и запуск:

    ```console
    mvn clean package
    java -jar target/adx-java-ingest-jar-with-dependencies.jar
    ```

    Результат должен быть аналогичен приведенному ниже:

    ```console
    Table dropped
    Table created
    Mapping created
    Waiting for ingestion to complete...
    ```

Подождите несколько минут, пока не завершится процесс приема. После успешного завершения вы увидите следующее сообщение журнала: `Ingestion completed successfully` . Вы можете выйти из программы на этом этапе и перейти к следующему шагу, не влияя на процесс приема, который уже поставлен в очередь.

## <a name="validate"></a>Проверить

Подождите от пяти до 10 минут, чтобы запланировать процесс приема и загрузить данные в Azure обозреватель данных. 

1. Войдите в [https://dataexplorer.azure.com](https://dataexplorer.azure.com) кластер и подключитесь к нему. 
1. Выполните следующую команду, чтобы получить количество записей в `StormEvents` таблице:
    
    ```kusto
    StormEvents | count
    ```

## <a name="troubleshoot"></a>Диагностика

1. Чтобы просмотреть ошибки приема в течение последних четырех часов, выполните следующую команду в базе данных: 

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. Чтобы просмотреть состояние всех операций приема за последние четыре часа, выполните следующую команду:

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать только что созданные ресурсы, выполните следующую команду в базе данных, чтобы удалить `StormEvents` таблицу.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов](write-queries.md)
