---
title: Прием данных с помощью пакета SDK Azure обозреватель данных Go
description: Из этой статьи вы узнаете, как принимать (загружать) данные в Azure обозреватель данных с помощью пакета SDK для Go.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 010ee029c4f16248b0f9249d7331da436439cdc0
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88072367"
---
# <a name="ingest-data-using-the-azure-data-explorer-go-sdk"></a>Прием данных с помощью пакета SDK для Azure обозреватель данных Go 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [GO](go-ingest-data.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Она предоставляет [клиентскую библиотеку пакета SDK для Go](kusto/api/golang/kusto-golang-client-library.md) для взаимодействия со службой Обозреватель данных Azure. [Пакет SDK для Go](https://github.com/Azure/azure-kusto-go) можно использовать для приема, управления и запроса данных в кластерах Azure обозреватель данных. 

В этой статье вы сначала создадите таблицу и сопоставление данных в тестовом кластере. Затем вы помещаете в очередь прием для кластера с помощью пакета SDK для Go и проверяете результаты.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Установите [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Установите [Go](https://golang.org/) со следующими [минимальными требованиями к пакету Go SDK](kusto/api/golang/kusto-golang-client-library.md#minimum-requirements). 
* Создайте [кластер Azure обозреватель данных и базу данных](create-cluster-database-portal.md).

## <a name="install-the-go-sdk"></a>Установка пакета SDK для Go

Пакет SDK для Azure обозреватель данных Go будет автоматически установлен при запуске [примера приложения, использующего [модули Go](https://golang.org/ref/mod). Если вы установили пакет SDK для Go для другого приложения, создайте модуль Go и получите пакет обозреватель данных Azure (с помощью `go get` ), например:

```console
go mod init foo.com/bar
go get github.com/Azure/azure-kusto-go/kusto
```

Зависимость пакета будет добавлена в `go.mod` файл. Используйте его в приложении go.

## <a name="review-the-code"></a>Просмотр кода

Этот раздел [кода](#review-the-code) является необязательным. Если вы хотите узнать, как работает код, вы можете ознакомиться с приведенными ниже фрагментами кода. В противном случае вы можете сразу перейти к разделу [Выполнение приложения](#run-the-application).

### <a name="authenticate"></a>Authenticate

Перед выполнением любых операций программа должна пройти проверку подлинности в службе обозреватель данных Azure.

```go
auth := kusto.Authorization{Config: auth.NewClientCredentialsConfig(clientID, clientSecret, tenantID)}
client, err := kusto.New(kustoEndpoint, auth)
```

Экземпляр [kusto. Авторизация](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Authorization) создается с использованием учетных данных субъекта-службы. Затем он используется для создания [kusto. Клиент](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client) с [новой](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#New]) функцией, которая также принимает конечную точку кластера.

### <a name="create-table"></a>Создать таблицу

Команда CREATE TABLE представлена [инструкцией Kusto](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Stmt). Для выполнения команд управления используется функция [руководства](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client.Mgmt) . Он используется для выполнения команды для создания таблицы. 

```go
func createTable(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createTableCommand))
    if err != nil {
        log.Fatal("failed to create table", err)
    }
    log.Printf("Table %s created in DB %s\n", kustoTable, kustoDB)
}
```

> [!TIP]
> Инструкция Kusto по умолчанию является константой для повышения безопасности. [`NewStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#NewStmt)принимает строковые константы. [`UnsafeStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#UnsafeStmt)API позволяет использовать неконстантные сегменты операторов, но это не рекомендуется.

Команда Kusto CREATE TABLE выглядит следующим образом:

```kusto
.create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
```

### <a name="create-mapping"></a>Создать сопоставление

Сопоставления данных используются во время приема для сопоставления входящих данных со столбцами в обозреватель данных таблицах Azure. Дополнительные сведения см. в разделе [сопоставление данных](kusto/management/mappings.md). Сопоставление создается таким же образом, как и таблица, с использованием `Mgmt` функции с именем базы данных и соответствующей командой. Полная команда доступна в [репозитории GitHub для примера](https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data/blob/main/main.go#L20).

```go
func createMapping(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createMappingCommand))
    if err != nil {
        log.Fatal("failed to create mapping - ", err)
    }
    log.Printf("Mapping %s created\n", kustoMappingRefName)
}
```

### <a name="ingest-data"></a>Прием данных

Прием помещается в очередь с помощью файла из существующего контейнера хранилища BLOB-объектов Azure. 

```go
func ingestFile(kc *kusto.Client, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable string) {
    kIngest, err := ingest.New(kc, kustoDB, kustoTable)
    if err != nil {
        log.Fatal("failed to create ingestion client", err)
    }
    blobStorePath := fmt.Sprintf(blobStorePathFormat, blobStoreAccountName, blobStoreContainer, blobStoreFileName, blobStoreToken)
    err = kIngest.FromFile(context.Background(), blobStorePath, ingest.FileFormat(ingest.CSV), ingest.IngestionMappingRef(kustoMappingRefName, ingest.CSV))

    if err != nil {
        log.Fatal("failed to ingest file", err)
    }
    log.Println("Ingested file from -", blobStorePath)
}
```

Клиент [приема](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion) создается с помощью [приема. Создать](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#New). Функция [фромфиле](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion.FromFile) используется для ссылки на URI хранилища BLOB-объектов Azure. Ссылочное имя сопоставления и тип данных передаются в виде [филеоптион](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#FileOption). 

## <a name="run-the-application"></a>Выполнение приложения

1. Клонировать пример кода из GitHub:

    ```console
    git clone https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data.git
    cd Azure-Data-Explorer-Go-SDK-example-to-ingest-data
    ```

1. Запустите пример кода, как показано в следующем фрагменте `main.go` : 

    ```go
    func main {
        ...
        dropTable(kc, kustoDB)
        createTable(kc, kustoDB)
        createMapping(kc, kustoDB)
        ingestFile(kc, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable)
        ...
    }
    ```

    > [!TIP]
    > Чтобы попробовать различные сочетания операций, можно раскомментировать или закомментировать соответствующие функции в `main.go` .

    При запуске примера кода выполняются следующие действия.
    
    1. **DROP TABLE**: `StormEvents` таблица удалена (если она существует).
    1. **Создание таблицы**: `StormEvents` таблица создана.
    1. **Создание сопоставления**: `StormEvents_CSV_Mapping` сопоставление создано.
    1. Прием **файлов**: CSV-файл (в хранилище BLOB-объектов Azure) помещается в очередь для приема.

1. Чтобы создать субъект-службу для проверки подлинности, используйте Azure CLI с командой [AZ AD SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) . Укажите в качестве сведений о субъекте-службе конечную точку кластера и имя базы данных в виде переменных среды, которые будут использоваться программой:

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export AZURE_SP_TENANT_ID="<replace with tenant>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. Запустите программу.

    ```console
    go run main.go
    ```

    Вы получите аналогичные выходные данные:

    ```console
    Connected to Azure Data Explorer
    Using database - testkustodb
    Failed to drop StormEvents table. Maybe it does not exist?
    Table StormEvents created in DB testkustodb
    Mapping StormEvents_CSV_Mapping created
    Ingested file from - https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D
    ```

## <a name="validate-and-troubleshoot"></a>Проверка и устранение неполадок

Подождите от 5 до 10 минут для запланированного приема, чтобы запланировать процесс приема и загрузить данные в Azure обозреватель данных. 

1. Войдите в [https://dataexplorer.azure.com](https://dataexplorer.azure.com) кластер и подключитесь к нему. Затем выполните следующую команду, чтобы получить количество записей в `StormEvents` таблице.

    ```kusto
    StormEvents | count
    ```

2. Выполните в своей базе данных следующую команду, чтобы проверить, не было ли в ней сбоев приема за последние четыре часа. Замените имя базы данных перед запуском.

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. Выполните следующую команду, чтобы узнать состояние всех операций приема за последние четыре часа. Замените имя базы данных перед запуском.

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете следовать нашим другим статьям, не заполняйте созданные ресурсы. В противном случае выполните следующую команду в базе данных, чтобы удалить `StormEvents` таблицу.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов](write-queries.md)
