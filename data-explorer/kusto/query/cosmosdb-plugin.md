---
title: подключаемый модуль cosmosdb_sql_request — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль cosmosdb_sql_request в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 09/11/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 035705e0a15ab686af16b9e8b2a00268db21d6a2
ms.sourcegitcommit: c140bc3bc984f861df0b85e672d2e685e6659a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90109993"
---
# <a name="cosmosdb_sql_request-plugin"></a>подключаемый модуль cosmosdb_sql_request

::: zone pivot="azuredataexplorer"

`cosmosdb_sql_request`Подключаемый модуль ОТПРАВЛЯЕТ SQL-запрос в конечную точку сети sql Cosmos DB и возвращает результаты запроса. Этот подключаемый модуль в основном предназначен для запросов к небольшим наборам данных, например, для добавления в данные ссылочных данных, хранящихся в [Azure Cosmos DB](/azure/cosmos-db/).

## <a name="syntax"></a>Синтаксис

`evaluate``cosmosdb_sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *склпараметерс* [ `,` *Параметры*]]`)`

## <a name="arguments"></a>Аргументы

|Имя аргумента | Описание | Обязательный или необязательный | 
|---|---|---|
| *ConnectionString* | `string`Литерал, указывающий строку подключения, указывающую на Cosmos DB коллекцию для запроса. Он должен включать *аккаунтендпоинт*, *базу данных*и *коллекцию*. Он может включать *AccountKey* , если для проверки подлинности используется главный ключ. <br> **Пример:** `'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/ ;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;'`| Обязательно |
| *SqlQuery*| `string`Литерал, указывающий выполняемый запрос. | Обязательно |
| *склпараметерс* | Постоянное значение типа `dynamic` , содержащее пары "ключ-значение" для передачи в качестве параметров вместе с запросом. Имена параметров должны начинаться с `@` . | Необязательно |
| *Параметры* | Постоянное значение типа `dynamic` , содержащее дополнительные параметры в виде пар "ключ-значение". | Необязательно |
|| ----*В число поддерживаемых параметров входят следующие.*-----
|      `armResourceId` | Получите ключ API из Azure Resource Manager <br> **Пример:** `/subscriptions/a0cd6542-7eaf-43d2-bbdd-b678a869aad1/resourceGroups/ cosmoddbresourcegrouput/providers/Microsoft.DocumentDb/databaseAccounts/cosmosdbacc`| 
|  `token` | Укажите маркер доступа Azure AD, используемый для проверки подлинности в Azure Resource Manager.
| `preferredLocations` | Управление областью, из которой запрашиваются данные. <br> **Пример:** `['East US']` | |  

## <a name="set-callout-policy"></a>Задать политику выноски

Подключаемый модуль делает выноски Cosmos DB. Убедитесь, что [Политика выноски](../management/calloutpolicy.md) кластера разрешает вызов типа `cosmosdb` в целевой *космосдбури*.

В следующем примере показано, как определить политику выноски для Cosmos DB. Рекомендуется ограничить его определенными конечными точками ( `my_endpoint1` , `my_endpoint2` ).

```kusto
[
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint1.documents.azure.com",
    "CanCall": true
  },
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint2.documents.azure.com",
    "CanCall": true
  }
]
```

В следующем примере показана команда ALTER выноска для `cosmosdb` *каллауттипе*

```kusto
.alter cluster policy callout @'[{"CalloutType": "cosmosdb", "CalloutUriRegex": "\\.documents\\.azure\\.com", "CanCall": true}]'
```

## <a name="examples"></a>Примеры

### <a name="query-cosmos-db"></a>Cosmos DB запроса

В следующем примере используется подключаемый модуль *cosmosdb_sql_request* для отправки SQL-запроса для выборки данных из Cosmos DB с помощью API SQL.

```kusto
evaluate cosmosdb_sql_request(
  'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
  'SELECT * from c')
```

### <a name="query-cosmos-db-with-parameters"></a>Запрос Cosmos DB с параметрами

В следующем примере используются параметры SQL Query и запрашиваются данные из альтернативного региона. Дополнительные сведения см. на веб-сайте [`preferredLocations`](/azure/cosmos-db/tutorial-global-distribution-sql-api?tabs=dotnetv2%2Capi-async#preferred-locations).

```kusto
evaluate cosmosdb_sql_request(
    'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
    "SELECT c.id, c.lastName, @param0 as Column0 FROM c WHERE c.dob >= '1970-01-01T00:00:00Z'",
    dynamic({'@param0': datetime(2019-04-16 16:47:26.7423305)}),
    dynamic({'preferredLocations': ['East US']}))
| where lastName == 'Smith'
```

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
