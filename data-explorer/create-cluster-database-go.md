---
title: Создание базы данных & кластера Azure обозреватель данных с помощью пакета SDK для Azure Go
description: Узнайте, как создавать, перечислять и удалять кластеры и базы данных Azure обозреватель данных с помощью пакета SDK для Azure go.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 51f44d1a3cd29121a99e442f4033176b00b9e09f
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349398"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-go"></a>Создание кластера Azure обозреватель данных и базы данных с помощью Go

> [!div class="op_single_selector"]
> * [Портал](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [GO](create-cluster-database-go.md)
> * [Шаблон ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer — это быстрая и полностью управляемая служба для аналитики большого объема потоковых данных в реальном времени, поступающих из приложений, а также с веб-сайтов, устройств Интернета вещей и т. д. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы должны принять (загрузить) данные в базы данных, чтобы к ним можно было выполнять запросы. 

В этой статье вы создадите кластер Azure обозреватель данных и базу данных с помощью [Go](https://golang.org/). Затем вы можете перечислить и удалить новый кластер и базу данных, а также выполнить операции с ресурсами.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free) Azure, прежде чем начинать работу.
* Установите [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Установите соответствующую версию go. Дополнительные сведения о поддерживаемых выпусках см. в разделе [пакет SDK для Azure Go](https://github.com/Azure/azure-sdk-for-go).

## <a name="review-the-code"></a>Просмотр кода

Этот раздел является необязательным. Если вы хотите узнать, как работает код, вы можете ознакомиться с приведенными ниже фрагментами кода. В противном случае вы можете сразу перейти к разделу [Выполнение приложения](#run-the-application).

### <a name="authentication"></a>Аутентификация

Перед выполнением любых операций программа должна пройти проверку подлинности в Azure обозреватель данных. [Тип проверки подлинности учетных данных клиента](/azure/developer/go/azure-sdk-authorization#use-environment-based-authentication) используется при проверке подлинности [. Неваусоризерфроменвиронмент](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromEnvironment) , который ищет следующие предварительно определенные переменные среды: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` , `AZURE_TENANT_ID` .

В следующем примере показано, как [kusto. Клустерсклиент](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient) создается с использованием этого метода:

```go
func getClustersClient(subscription string) kusto.ClustersClient {
    client := kusto.NewClustersClient(subscription)
    authR, err := auth.NewAuthorizerFromEnvironment()
    if err != nil {
        log.Fatal(err)
    }
    client.Authorizer = authR

    return client
}
```

> [!TIP]
> Используйте проверку [подлинности. Функция Неваусоризерфромкливисресаурце](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) для локальной разработки, если Azure CLI установлена и настроена проверка подлинности.

### <a name="create-cluster"></a>Создание кластера

Используйте функцию [CreateOrUpdate](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient.CreateOrUpdate) в `kusto.ClustersClient` для создания нового кластера Azure обозреватель данных. Дождитесь завершения процесса, прежде чем проверять результаты.

```go
func createCluster(sub, name, location, rgName string) {
    ...
    result, err := client.CreateOrUpdate(ctx, rgName, name, kusto.Cluster{Location: &location, Sku: &kusto.AzureSku{Name: kusto.DevNoSLAStandardD11V2, Capacity: &numInstances, Tier: kusto.Basic}})
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
}
```

### <a name="list-clusters"></a>список кластеров

Чтобы получить kusto, используйте функцию [листбиресаурцеграуп](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient.ListByResourceGroup) в `kusto.ClustersClient` [. Клустерлистресулт](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClusterListResult) , который затем перебирается для отображения выходных данных в табличном формате.


```go
func listClusters(sub, rgName string) {
    ...
    result, err := getClustersClient(sub).ListByResourceGroup(ctx, rgName)
    ...
    for _, c := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="create-database"></a>Создание базы данных

Используйте функцию [CreateOrUpdate](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient.CreateOrUpdate) в [kusto. Датабасесклиент](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient) , чтобы создать новую базу данных Azure обозреватель данных в существующем кластере. Дождитесь завершения процесса, прежде чем проверять результаты.


```go
func createDatabase(sub, rgName, clusterName, location, dbName string) {
    future, err := client.CreateOrUpdate(ctx, rgName, clusterName, dbName, kusto.ReadWriteDatabase{Kind: kusto.KindReadWrite, Location: &location})
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    ...
}
```

### <a name="list-databases"></a>Список баз данных

Для получения kusto используйте функцию [листбиклустер](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient.ListByCluster) в `kusto.DatabasesClient` [. Датабаселистресулт](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabaseListResult) , который затем перебирается для отображения выходных данных в табличном формате.


```go
func listDatabases(sub, rgName, clusterName string) {
    result, err := getDBClient(sub).ListByCluster(ctx, rgName, clusterName)
    ...
    for _, db := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="delete-database"></a>Удаление базы данных

Используйте функцию [Delete](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#DatabasesClient.Delete) для, `kusto.DatabasesClient` чтобы удалить существующую базу данных в кластере. Дождитесь завершения процесса, прежде чем проверять результаты.

```go
func deleteDatabase(sub, rgName, clusterName, dbName string) {
    ...
    future, err := getDBClient(sub).Delete(ctx, rgName, clusterName, dbName)
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

### <a name="delete-cluster"></a>Удаление кластера

Чтобы удалить кластер, используйте функцию [Delete](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v0.0.0-20200513030755-ac906323d9fe/services/kusto/mgmt/2020-02-15/kusto?tab=doc#ClustersClient.Delete) на a `kusto.ClustersClient` . Дождитесь завершения процесса, прежде чем проверять результаты.

```go
func deleteCluster(sub, clusterName, rgName string) {
    result, err := client.Delete(ctx, rgName, clusterName)
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

## <a name="run-the-application"></a>Выполнение приложения

При выполнении примера кода, как есть, выполняются следующие действия.
    
1. Создается кластер Azure обозреватель данных.
1. В списке указаны все кластеры обозреватель данных Azure в указанной группе ресурсов.
1. В существующем кластере создается база данных обозреватель данных Azure.
1. В списке перечислены все базы данных в указанном кластере.
1. База данных удалена.
1. Кластер удален.


    ```go
    func main() {
        createCluster(subscription, clusterNamePrefix+clusterName, location, rgName)
        listClusters(subscription, rgName)
        createDatabase(subscription, rgName, clusterNamePrefix+clusterName, location, dbNamePrefix+databaseName)
        listDatabases(subscription, rgName, clusterNamePrefix+clusterName)
        deleteDatabase(subscription, rgName, clusterNamePrefix+clusterName, dbNamePrefix+databaseName)
        deleteCluster(subscription, clusterNamePrefix+clusterName, rgName)
    }
    ```

    > [!TIP]
    > Чтобы попробовать различные сочетания операций, можно раскомментировать и закомментировать соответствующие функции при `main.go` необходимости.

1. Клонировать пример кода из GitHub:

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-go-cluster-management.git
    cd azure-data-explorer-go-cluster-management
    ```

1. Программа выполняет проверку подлинности с использованием учетных данных клиента. Чтобы создать субъект-службу, используйте команду Azure CLI [AZ AD SP Create/for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) . Сохраните идентификатор клиента, секрет клиента и сведения об ИДЕНТИФИКАТОРе клиента для использования на следующем шаге.

1. Экспортируйте необходимые переменные среды, включая сведения о субъекте-службе. Введите идентификатор подписки, группу ресурсов и регион, в которых требуется создать кластер.

    ```console
    export AZURE_CLIENT_ID="<enter service principal client ID>"
    export AZURE_CLIENT_SECRET="<enter service principal client secret>"
    export AZURE_TENANT_ID="<enter tenant ID>"

    export SUBSCRIPTION="<enter subscription ID>"
    export RESOURCE_GROUP="<enter resource group name>"
    export LOCATION="<enter azure location e.g. Southeast Asia>"

    export CLUSTER_NAME_PREFIX="<enter prefix (cluster name will be [prefix]-ADXTestCluster)>"
    export DATABASE_NAME_PREFIX="<enter prefix (database name will be [prefix]-ADXTestDB)>"
    ```
    
    > [!TIP]
    > Вы, скорее всего, будете использовать переменные среды в рабочих сценариях для предоставления учетных данных приложению. Как упоминалось в [обзоре кода](#review-the-code)для локальной разработки, используйте проверку [подлинности. Неваусоризерфромкливисресаурце](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) , если Azure CLI установлен и настроен для проверки подлинности. В этом случае вам не нужно создавать субъект-службу.


1. Запустите программу.

    ```console
    go run main.go
    ```

    Вы получите результат, аналогичный приведенному ниже:

    ```console
    waiting for cluster creation to complete - fooADXTestCluster
    created cluster fooADXTestCluster
    listing clusters in resource group <your resource group>
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    |       NAME        |  STATE  |    LOCATION    | INSTANCES |                            URI                           |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    | fooADXTestCluster | Running | Southeast Asia |         1 | https://fooADXTestCluster.southeastasia.kusto.windows.net |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    
    waiting for database creation to complete - barADXTestDB
    created DB fooADXTestCluster/barADXTestDB with ID /subscriptions/<your subscription ID>/resourceGroups/<your resource group>/providers/Microsoft.Kusto/Clusters/fooADXTestCluster/Databases/barADXTestDB and type Microsoft.Kusto/Clusters/Databases
    
    listing databases in cluster fooADXTestCluster
    +--------------------------------+-----------+----------------+------------------------------------+
    |              NAME              |   STATE   |    LOCATION    |                TYPE                |
    +--------------------------------+-----------+----------------+------------------------------------+
    | fooADXTestCluster/barADXTestDB | Succeeded | Southeast Asia | Microsoft.Kusto/Clusters/Databases |
    +--------------------------------+-----------+----------------+------------------------------------+
    
    waiting for database deletion to complete - barADXTestDB
    deleted DB barADXTestDB from cluster fooADXTestCluster

    waiting for cluster deletion to complete - fooADXTestCluster
    deleted ADX cluster fooADXTestCluster from resource group <your resource group>
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не удалили кластер программным способом с помощью примера кода в этой статье, удалите их вручную с помощью [Azure CLI](create-cluster-database-cli.md#clean-up-resources).

## <a name="next-steps"></a>Дальнейшие действия

[Прием данных с помощью пакета SDK для Azure обозреватель данных Go](go-ingest-data.md)