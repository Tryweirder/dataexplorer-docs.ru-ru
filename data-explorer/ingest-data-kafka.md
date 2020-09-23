---
title: Прием данных из Kafka в Azure Data Explorer
description: Из этой статьи вы узнаете, как принимать (загружать) данные в Azure обозреватель данных из Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 14f4ed38ecb2e5b4a94dad8a73fb43ea3ff1e5ee
ms.sourcegitcommit: c8256390d745e345f44d401e33e775702d62721d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91007816"
---
# <a name="ingest-data-from-apache-kafka-into-azure-data-explorer"></a>Прием данных из Apache Kafka в Azure обозреватель данных
 
Azure обозреватель данных поддерживает прием [данных](ingest-data-overview.md) от [Apache Kafka](http://kafka.apache.org/documentation/). Apache Kafka — это распределенная платформа потоковой передачи для создания конвейеров потоковых данных в режиме реального времени, которые надежно перемещают данные между системами или приложениями. [Kafka Connect](https://docs.confluent.io/3.0.1/connect/intro.html#kafka-connect) — это инструмент для масштабируемой и надежной потоковой передачи данных между Apache Kafka и другими системами данных. Приемник Azure обозреватель данных [Kafka](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) выступает в качестве соединителя от Kafka и не требует использования кода. Скачайте JAR-файл соединителя приемника из [репозитория git](https://github.com/Azure/kafka-sink-azure-kusto/releases) или [концентратора соединителей Fluent](https://www.confluent.io/hub/microsoftcorporation/kafka-sink-azure-kusto).

В этой статье показано, как принимать данные с помощью Kafka в Azure обозреватель данных, используя автономную установку DOCKER для упрощения кластера Kafka и установки кластера Kafka Connector. 

Дополнительные сведения см. в разделе [репозиторий Git](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) и [сведения о версиях](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md#13-major-version-specifics)для соединителя.

## <a name="prerequisites"></a>Предварительные требования

* Создайте [учетную запись Microsoft Azure](https://docs.microsoft.com/azure/).
* Установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Установите [DOCKER](https://docs.docker.com/get-docker/) и [DOCKER Compose](https://docs.docker.com/compose/install).
* [Создайте кластер Azure обозреватель данных и базу данных в портал Azure,](create-cluster-database-portal.md) используя политики кэша и хранения по умолчанию.

## <a name="create-an-azure-active-directory-service-principal"></a>Создание субъекта-службы Azure Active Directory

Субъект-службу Azure Active Directory можно создать с помощью [портал Azure](/azure/active-directory/develop/howto-create-service-principal-portal) или программно, как показано в следующем примере.

Этот субъект-служба будет удостоверением, используемым соединителем для записи в таблицу Azure обозреватель данных. Позже мы предоставим разрешения для этого субъекта-службы для доступа к Azure обозреватель данных.

1. Войдите в подписку Azure с помощью Azure CLI. Затем выполните аутентификацию в браузере.

   ```azurecli-interactive
   az login
   ```


1. Выберите подписку, которую вы хотите использовать для запуска лаборатории. Этот шаг необходим при наличии нескольких подписок.

   ```azurecli-interactive
   az account set --subscription YOUR_SUBSCRIPTION_GUID
   ```

1. Создайте субъект-службу. В этом примере вызывается субъект-служба `kusto-kafka-spn` .

   ```azurecli-interactive
   az ad sp create-for-rbac -n "kusto-kafka-spn"
   ```

1. Вы получите ответ JSON, как показано ниже. Скопируйте `appId` , `password` и, `tenant` так как они понадобятся в последующих шагах.

    ```json
    {
      "appId": "fe7280c7-5705-4789-b17f-71a472340429",
      "displayName": "kusto-kafka-spn",
      "name": "http://kusto-kafka-spn",
      "password": "29c719dd-f2b3-46de-b71c-4004fb6116ee",
      "tenant": "42f988bf-86f1-42af-91ab-2d7cd011db42"
    }
    ```

## <a name="create-a-target-table-in-azure-data-explorer"></a>Создание целевой таблицы в обозревателе данных Azure

1. Войдите на [портал Azure](https://portal.azure.com)

1. Перейдите к кластеру Azure обозреватель данных.

1. Создайте таблицу с именем `Storms` , используя следующую команду:

    ```kusto
    .create table Storms (StartTime: datetime, EndTime: datetime, EventId: int, State: string, EventType: string, Source: string)
    ```

    :::image type="content" source="media/ingest-data-kafka/create-table.png" alt-text="Создание таблицы на портале обозреватель данных Azure ":::
    
1. Создайте соответствующее сопоставление таблицы `Storms_CSV_Mapping` для полученных данных с помощью следующей команды:
    
    ```kusto
    .create table Storms ingestion csv mapping 'Storms_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EventId","datatype":"int","Ordinal":2},{"Name":"State","datatype":"string","Ordinal":3},{"Name":"EventType","datatype":"string","Ordinal":4},{"Name":"Source","datatype":"string","Ordinal":5}]'
    ```    

1. Создайте в таблице политику приема пакетов для настраиваемой задержки приема.

    > [!TIP]
    > [Политика пакетной обработки приема](kusto/management/batchingpolicy.md) — это оптимизатор производительности, включающий три параметра. Первый параметр запускает прием данных в таблицу Azure обозреватель данных.

    ```kusto
    .alter table Storms policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:15", "MaximumNumberOfItems": 100, "MaximumRawDataSizeMB": 300}'
    ```

1. Используйте субъект-службу, чтобы [создать субъект-службу Azure Active Directory](#create-an-azure-active-directory-service-principal) , чтобы предоставить разрешение на работу с базой данных.

    ```kusto
    .add database YOUR_DATABASE_NAME admins  ('aadapp=YOUR_APP_ID;YOUR_TENANT_ID') 'AAD App'
    ```

## <a name="run-the-lab"></a>Запуск лаборатории

Приведенная ниже лабораторная работа призвана помочь вам в создании данных, настройке соединителя Kafka и потоковой передаче этих данных в Azure обозреватель данных с помощью соединителя. Затем можно просмотреть полученные данные.

### <a name="clone-the-git-repo"></a>Клонирование репозитория Git

Клонировать [репозиторий](https://github.com/Azure/azure-kusto-labs)Git лаборатории.

1. Создайте локальный каталог на компьютере.

    ```
    mkdir ~/kafka-kusto-hol
    cd ~/kafka-kusto-hol
    ```

1. Клонировать репозиторий.

    ```shell
    cd ~/kafka-kusto-hol
    git clone https://github.com/Azure/azure-kusto-labs
    cd azure-kusto-labs/kafka-integration/dockerized-quickstart
    ```

#### <a name="contents-of-the-cloned-repo"></a>Содержимое клонированного репозитория

Выполните следующую команду, чтобы получить список содержимого клонированного репозитория:

```
cd ~/kafka-kusto-hol/azure-kusto-labs/kafka-integration/dockerized-quickstart
tree
```

Результат этого поиска:

```
├── README.md
├── adx-query.png
├── adx-sink-config.json
├── connector
│   └── Dockerfile
├── docker-compose.yaml
└── storm-events-producer
    ├── Dockerfile
    ├── StormEvents.csv
    ├── go.mod
    ├── go.sum
    ├── kafka
    │   └── kafka.go
    └── main.go
 ```

### <a name="review-the-files-in-the-cloned-repo"></a>Проверка файлов в клонированном репозитории

В следующих разделах объясняются важные части файлов в дереве файлов, приведенном выше.

#### <a name="adx-sink-configjson"></a>adx-sink-config.jsна

Этот файл содержит файл свойств приемника Kusto, в который вы будете обновлять определенные сведения о конфигурации:
 
```json
{
    "name": "storm",
    "config": {
        "connector.class": "com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector",
        "flush.size.bytes": 10000,
        "flush.interval.ms": 10000,
        "tasks.max": 1,
        "topics": "storm-events",
        "kusto.tables.topics.mapping": "[{'topic': 'storm-events','db': '<enter database name>', 'table': 'Storms','format': 'csv', 'mapping':'Storms_CSV_Mapping'}]",
        "aad.auth.authority": "<enter tenant ID>",
        "aad.auth.appid": "<enter application ID>",
        "aad.auth.appkey": "<enter client secret>",
        "kusto.url": "https://ingest-<name of cluster>.<region>.kusto.windows.net",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.storage.StringConverter"
    }
}
```

Замените значения следующих атрибутов в соответствии с настройками обозреватель данных Azure: `aad.auth.authority` , `aad.auth.appid` , `aad.auth.appkey` , `kusto.tables.topics.mapping` (имя базы данных) и `kusto.url` .

#### <a name="connector---dockerfile"></a>Соединитель — Dockerfile

Этот файл содержит команды для создания образа DOCKER для экземпляра соединителя.  Он включает в себя загрузку соединителя из каталога выпусков репозитория Git.

#### <a name="storm-events-producer-directory"></a>Огонь — события — Каталог производителя

В этом каталоге есть программа Go, которая считывает локальный файл "StormEvents.csv" и публикует данные в разделе Kafka.

#### <a name="docker-composeyaml"></a>DOCKER-создание. YAML

```yaml
version: "2"
services:
  zookeeper:
    image: debezium/zookeeper:1.2
    ports:
      - 2181:2181
  kafka:
    image: debezium/kafka:1.2
    ports:
      - 9092:9092
    links:
      - zookeeper
    depends_on:
      - zookeeper
    environment:
      - ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
  kusto-connect:
    build:
      context: ./connector
      args:
        KUSTO_KAFKA_SINK_VERSION: 1.0.1
    ports:
      - 8083:8083
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - BOOTSTRAP_SERVERS=kafka:9092
      - GROUP_ID=adx
      - CONFIG_STORAGE_TOPIC=my_connect_configs
      - OFFSET_STORAGE_TOPIC=my_connect_offsets
      - STATUS_STORAGE_TOPIC=my_connect_statuses
  events-producer:
    build:
      context: ./storm-events-producer
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - KAFKA_BOOTSTRAP_SERVER=kafka:9092
      - KAFKA_TOPIC=storm-events
      - SOURCE_FILE=StormEvents.csv
```

### <a name="start-the-containers"></a>Запуск контейнеров

1. В терминале запустите контейнеры:
    
    ```shell
    docker-compose up
    ```

    Приложение Producer начнет отправлять события в `storm-events` раздел. 
    Должны отобразиться журналы, аналогичные следующим журналам:

    ```shell
    ....
    events-producer_1  | sent message to partition 0 offset 0
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 00:00:00.0000000,13208,NORTH CAROLINA,Thunderstorm Wind,Public
    events-producer_1  | 
    events-producer_1  | sent message to partition 0 offset 1
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 05:00:00.0000000,23358,WISCONSIN,Winter Storm,COOP Observer
    ....
    ```
    
1. Чтобы проверить журналы, выполните следующую команду в отдельном терминале:

    ```shell
    docker-compose logs -f | grep kusto-connect
    ```
    
### <a name="start-the-connector"></a>Запуск соединителя

Используйте вызов Kafka Connect RESTFUL для запуска соединителя.

1. В отдельном терминале запустите задачу-приемник с помощью следующей команды:

    ```shell
    curl -X POST -H "Content-Type: application/json" --data @adx-sink-config.json http://localhost:8083/connectors
    ```
    
1. Чтобы проверить состояние, выполните следующую команду в отдельном терминале:
    
    ```shell
    curl http://localhost:8083/connectors/storm/status
    ```

Соединитель начнет процессы приема в очередь в Azure обозреватель данных.

> [!NOTE]
> Если у вас возникли проблемы с подключением к соединителю, [Создайте вопрос](https://github.com/Azure/kafka-sink-azure-kusto/issues).

## <a name="query-and-review-data"></a>Запрос и проверка данных

### <a name="confirm-data-ingestion"></a>Подтверждение приема данных

1. Дождитесь поступления данных в `Storms` таблицу. Чтобы подтвердить перенос данных, проверьте число строк:
    
    ```kusto
    Storms | count
    ```

1. Убедитесь в отсутствии сбоев в процессе приема:

    ```kusto
    .show ingestion failures
    ```
    
    После просмотра данных Попробуйте несколько запросов. 

### <a name="query-the-data"></a>Запрос данных

1. Чтобы просмотреть все записи, выполните следующий [запрос](write-queries.md):
    
    ```kusto
    Storms
    ```

1. Используйте `where` и `project` для фильтрации конкретных данных:
    
    ```kusto
    Storms
    | where EventType == 'Drought' and State == 'TEXAS'
    | project StartTime, EndTime, Source, EventId
    ```
    
1. Используйте [`summarize`](https://docs.microsoft.com/azure/data-explorer/write-queries#summarize) оператор:

    ```kusto
    Storms
    | summarize event_count=count() by State
    | where event_count > 10
    | project State, event_count
    | render columnchart
    ```
    
    :::image type="content" source="media/ingest-data-kafka/kusto-query.png" alt-text="Результаты гистограммы запроса Kafka в Azure обозреватель данных":::

Дополнительные примеры запросов и рекомендации см. в статье [написание запросов для Azure обозреватель данных](write-queries.md) и [документации по языку запросов Kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query/).

## <a name="reset"></a>Reset

Чтобы выполнить сброс, выполните следующие действия.

1. Останавливает контейнеры ( `docker-compose down -v` )
1. Удалить (`drop table Storms`)
1. Повторное создание `Storms` таблицы
1. Повторное создание сопоставления таблиц
1. Перезапустить контейнеры ( `docker-compose up` )

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить ресурсы обозреватель данных Azure, выполните команду [AZ Cluster Delete](https://docs.microsoft.com/cli/azure/kusto/cluster#az-kusto-cluster-delete) или [AZ Kusto Database Delete](https://docs.microsoft.com/cli/azure/kusto/database#az-kusto-database-delete):

```azurecli-interactive
az kusto cluster delete -n <cluster name> -g <resource group name>
az kusto database delete -n <database name> --cluster-name <cluster name> -g <resource group name>
```

## <a name="next-steps"></a>Next Steps

* Дополнительные сведения об [архитектуре больших данных](/azure/architecture/solution-ideas/articles/big-data-azure-data-explorer).
* Узнайте [, как получать демонстрационные данные в формате JSON в обозреватель данных Azure](https://docs.microsoft.com/azure/data-explorer/ingest-json-formats?tabs=kusto-query-language).
* Дополнительные Kafka Labs:
   * [Практическое занятие приема от Fluent облака Kafka в распределенном режиме](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/confluent-cloud/README.md)
   * [Практическое занятие приема из HDInsight Kafka в распределенном режиме](https://github.com/Azure/azure-kusto-labs/tree/master/kafka-integration/distributed-mode/hdinsight-kafka)
   * [Практическое занятие для приема из Kafka IaaS в AKS в распределенном режиме](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/distributed-mode/confluent-kafka/README.md)
