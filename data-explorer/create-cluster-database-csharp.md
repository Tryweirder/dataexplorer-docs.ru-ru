---
title: 'Создание кластера Azure Data Explorer & DB с помощью C #'
description: Сведения о создании кластера и базы данных Azure Data Explorer с использованием C#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0cdd649e6d031865e1d48a3cc38f58dbdb63392c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497061"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Создание кластера и базы данных Azure Data Explorer с помощью C#

> [!div class="op_single_selector"]
> * [Портал](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Шаблон Azure Resource Manager](create-cluster-database-resource-manager.md)

Azure Data Explorer — это быстрая и полностью управляемая служба для аналитики большого объема потоковых данных в реальном времени, поступающих из приложений, а также с веб-сайтов, устройств Интернета вещей и т. д. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы должны принять (загрузить) данные в базы данных, чтобы к ним можно было выполнять запросы. В этой статье вы создаете кластер и базу данных с помощью C..

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что вы **включите разработку Azure** во время установки Visual Studio.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Аутентификация
Для запуска примеров в этой статье нам необходимо приложение Azure AD и директор службы, которые могут получить доступ к ресурсам. Проверьте [создание приложения Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) для создания бесплатного приложения Azure AD и добавления ролевых заданий в области подписки. Он также `Directory (tenant) ID`показывает, как `Application ID`получить `Client Secret`, , и .

## <a name="create-the-azure-data-explorer-cluster"></a>Создание кластера Azure Data Explorer

1. Создайте кластер, используя приведенный ниже код:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | clusterName | *mykustocluster* | Необходимое имя кластера.|
   | skuName | *Standard_D13_v2* | Номер SKU, который будет использоваться для кластера. |
   | Уровень | *Standard Edition* | Уровень SKU. |
   | Емкость | *number* | Количество экземпляров кластера. |
   | имя_группы_ресурсов | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |

    > [!NOTE]
    > **Создание кластера** является длительной операцией, поэтому настоятельно рекомендуется использовать CreateOrUpdateAsync, а не CreateOrUpdate. 

1. Чтобы проверить, успешно ли создан кластер, можно выполнить следующую команду:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Если результат содержит параметр `ProvisioningState` со значением `Succeeded`, это означает, что кластер создан успешно.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Создание базы данных в кластере Azure Data Explorer

1. Создайте базу данных, используя приведенный ниже код:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | clusterName | *mykustocluster* | Имя кластера, в котором необходимо создать базу данных.|
   | databaseName | *mykustodatabase* | Имя базы данных.|
   | имя_группы_ресурсов | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |
   | softDeletePeriod | *3650:00:00:00* | Время, на протяжении которого данные будут храниться таким образом, чтобы они были доступны для запроса. |
   | hotCachePeriod | *3650:00:00:00* | Время, на протяжении которого данные будут храниться в кэше. |

2. Выполните следующую команду, чтобы просмотреть созданную базу данных:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Теперь у вас есть кластер и база данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

* Если вы планируете следовать нашим другим статьям, сохраните созданные ресурсы.
* Чтобы очистить ресурсы, удалите кластер. При удалении кластера также удаляются все содержащиеся в нем базы данных. Для удаления кластера используйте следующую команду:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Следующие шаги

* [Прием данных с помощью пакета SDK .NET Standard для Azure Data Explorer (предварительная версия)](net-standard-ingest-data.md)
