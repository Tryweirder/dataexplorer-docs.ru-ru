---
title: Подключение к обозреватель данных Azure из Azure Databricks
description: В этом разделе показано, как использовать Azure Databricks для доступа к данным из обозреватель данных Azure.
author: manojraheja
ms.author: maraheja
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 11db44424b86e4ca946ea104301bcd074797ca40
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863206"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks"></a>Подключение к обозреватель данных Azure из Azure Databricks

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) — это платформа аналитики на основе Apache Spark, оптимизированная для платформы Microsoft Azure. В этой статье показано, как использовать Azure Databricks для доступа к данным из обозреватель данных Azure. Существует несколько способов выполнения проверки подлинности в Azure Data Explorer, включая имя пользователя устройства и приложение Azure Active Directory (Azure AD).
 
## <a name="prerequisites"></a>Предварительные условия

- [Создайте кластер Azure обозреватель данных и базу данных](create-cluster-database-portal.md).
- [Создайте рабочую область Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). В разделе **Служба Azure Databricks** в раскрывающемся списке **Ценовая категория** выберите **Премиум**. Это дает возможность использовать секреты Azure Databricks для хранения учетных данных и ссылаться на них в записных книжках и заданиях.

- [Создайте кластер](https://docs.azuredatabricks.net/user-guide/clusters/create.html) в Azure Databricks с параметрами по умолчанию.

 ## <a name="install-the-kusto-spark-connector-on-your-azure-databricks-cluster"></a>Установка соединителя Kusto Spark в кластере Azure Databricks

Чтобы установить в кластере Azure Databricks [Spark-kusto-Connector](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector) :

1. Перейдите в рабочую область Azure Databricks и [создайте библиотеку](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
1. Выполните поиск пакета *Spark-kusto-Connector* в Maven Central, установите последнюю версию и подключитесь к кластеру. 

## <a name="connect-to-azure-data-explorer-by-using-a-device-authentication"></a>Подключение к обозреватель данных Azure с помощью проверки подлинности устройства

[Пример кода](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py).

## <a name="connect-to-azure-data-explorer-by-using-an-azure-ad-app"></a>Подключение к Azure обозреватель данных с помощью приложения Azure AD

1. Создайте приложение Azure AD, выполнив [подготовку приложения Azure AD](kusto/management/access-control/how-to-provision-aad-app.md).
1. Предоставьте доступ к Azure AD App в вашей базе данных Azure Data Explorer следующим образом:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | имя базы данных |
    | ```AAD App ID``` | идентификатор приложения Azure AD |
    | ```AAD Tenant ID``` | идентификатор клиента Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Поиск идентификатора клиента Azure AD

Чтобы проверить подлинность приложения, Azure Data Explorer использует идентификатор клиента Azure AD. Чтобы найти идентификатор клиента, используйте следующий URL-адрес. Замените его доменом *имя_домена*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Например, если ваш домен называется *contoso.com*, URL-адрес будет следующим: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Чтобы просмотреть результаты выберите этот URL-адрес. Первая строка выглядит следующим образом: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Ваш идентификатор клиента — `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key-optional"></a>Хранение и защита идентификатора и ключа приложения Azure AD (необязательно)  

Храните и защищайте идентификатор и ключ приложения Azure AD с помощью [секретов](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) Azure Databricks следующим образом:

1. [Настройте интерфейс командной строки](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Установите интерфейс командной строки](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Настройка проверки подлинности](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Настройте [секреты](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) с помощью приведенных ниже примеров команд:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="sample-code"></a>Пример кода

1. [Пример кода](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/pyKusto.py). 
1. Обновите значения заполнителей, указав имя кластера, имя базы данных, имя таблицы, идентификатор клиента Azure AD, идентификатор приложения AAD и ключ приложения AAD. Если вы храните свои учетные данные в хранилище секретов для данных в подмодулях, обновите код, чтобы получить значения из дбутилс.
