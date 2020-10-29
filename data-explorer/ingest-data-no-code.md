---
title: Руководство по Прием данных мониторинга в Azure Data Explorer без создания кода
description: В этом учебнике описано, как принимать данные мониторинга в Azure Data Explorer без необходимости написания кода, а также как создавать к ним запросы.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: fa4c47994ac96486fe6889e162fe917a5cef3f3e
ms.sourcegitcommit: 88291fd9cebc26e5210463cb95be5540bf84eef8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437450"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Руководство по приему данных мониторинга и созданию к ним запросов в Azure Data Explorer 

Из этого руководства вы узнаете, как принимать данные из журналов диагностики и действий в кластер Azure Data Explorer без необходимости писать код. Этот простой метод приема позволяет вам оперативно начать отправку запросов в Azure Data Explorer для анализа данных.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создавать таблицы и сопоставления приема в базе данных Azure Data Explorer;
> * форматировать принятые данные с помощью политики обновления;
> * создавать [концентраторы событий](/azure/event-hubs/event-hubs-about) и подключать их к Azure Data Explorer;
> * выполнять потоковую передачу данных в концентратор событий из [журналов и метрик диагностики Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) и [журналов действий](/azure/azure-monitor/platform/activity-logs-overview) Azure Monitor;
> * создавать запросы к принятым данным с помощью Azure Data Explorer.

> [!NOTE]
> Создавайте все ресурсы в одном расположении или регионе Azure. 

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Кластер и база данных Azure Data Explorer](create-cluster-database-portal.md). В этом руководстве используется имя базы данных *TestDatabase* .

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Поставщик данных Azure Monitor — журналы и метрики диагностики и журналы действий

Просмотрите и проанализируйте приведенные ниже данные, предоставленные журналами и метриками диагностики, а также журналами действий для службы Azure Monitor. На основе этих схем данных вы создадите конвейер приема. Обратите внимание, что для каждого события в журнале имеется массив записей. Позже в этом учебнике этот массив записей будет разделен.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Примеры журналов и метрик диагностики, а также журналов действий

Журналы и метрики диагностики Azure, а также журналы действий создаются службой Azure. Они предоставляют данные о работе службы. 

# <a name="diagnostic-metrics"></a>[Метрики диагностики](#tab/diagnostic-metrics)
#### <a name="example"></a>Пример

Метрики диагностики агрегируются по интервалам времени в 1 минуту. Ниже приведен пример схемы события для метрики Azure Data Explorer (на основе длительности запроса):

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[Журналы диагностики](#tab/diagnostic-logs)
#### <a name="example"></a>Пример

Ниже приведен пример [журнала приема диагностики](using-diagnostic-logs.md#diagnostic-logs-schema) Azure Data Explorer:

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[Журналы действий](#tab/activity-logs)
#### <a name="example"></a>Пример

Журналы действий Azure являются журналами уровня подписки, предоставляющими информацию об операциях, выполненных в ресурсах в подписке. Ниже приведен пример события журнала действия для проверки доступа:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Настройка конвейера приема данных в Azure Data Explorer

Настройка конвейера Azure Data Explorer включает несколько этапов, в том числе [создание таблиц и прием данных](ingest-sample-data.md#ingest-data). Вы также можете работать с данными, сопоставлять и обновлять их.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Подключение к пользовательскому веб-интерфейсу Azure Data Explorer

В базе данных *TestDatabase* для Azure Data Explorer выберите пункт **Запрос** , чтобы открыть пользовательский веб-интерфейс Azure Data Explorer.

![Страница запроса](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Создание целевых таблиц

Структура журналов Azure Monitor не является табличной. Вам потребуется обработать данные и развернуть каждое событие в одну или несколько записей. Необработанные данные будут поступать в промежуточную таблицу с именем *ActivityLogsRawRecords* для журналов действий и *DiagnosticRawRecords* для журналов и метрик диагностики. При этом данные будут обработаны и развернуты. Затем на основе политики обновления развернутые данные будут поступать в таблицу *ActivityLogs* для журналов действий, *DiagnosticMetrics* для метрик диагностики и *DiagnosticLogs* для журналов диагностики. Это означает, что вам нужно создать две отдельные таблицы для приема журналов действий и три отдельные таблицы для приема журналов и метрик диагностики.

С помощью пользовательского веб-интерфейса Azure Data Explorer создайте целевые таблицы в базе данных Azure Data Explorer.

# <a name="diagnostic-metrics"></a>[Метрики диагностики](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Создание таблиц для метрик диагностики

1. Создайте в базе данных *TestDatabase* таблицу с именем *DiagnosticMetrics* для хранения записей метрик диагностики. Для этого используйте следующую команду `.create table`.

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Выберите **Выполнить** , чтобы создать таблицу.

    ![Выполнение запроса](media/ingest-data-no-code/run-query.png)

1. С помощью следующего запроса создайте в базе данных *TestDatabase* промежуточную таблицу данных с именем *DiagnosticRawRecords* для обработки данных: Выберите **Выполнить** , чтобы создать таблицу.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Задайте нулевую [политику хранения](kusto/management/retention-policy.md) для промежуточной таблицы:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Журналы диагностики](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Создание таблиц для журналов диагностики 

1. Создайте в базе данных *TestDatabase* таблицу с именем *DiagnosticLogs* для хранения записей журнала диагностики. Для этого используйте следующую команду `.create table`.

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Выберите **Выполнить** , чтобы создать таблицу.

1. С помощью следующего запроса создайте в базе данных *TestDatabase* промежуточную таблицу данных с именем *DiagnosticRawRecords* для обработки данных: Выберите **Выполнить** , чтобы создать таблицу.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Задайте нулевую [политику хранения](kusto/management/retention-policy.md) для промежуточной таблицы:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Журналы действий](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Создание таблиц для журналов действий 

1. Создайте таблицу с именем *ActivityLogs* в базе данных *TestDatabase* , которая будет принимать записи журнала действий. Выполните следующий запрос Azure Data Explorer, чтобы создать эту таблицу:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Создайте в базе данных *TestDatabase* промежуточную таблицу данных с именем *ActivityLogsRawRecords* для обработки данных:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Задайте нулевую [политику хранения](kusto/management/retention-policy.md) для промежуточной таблицы:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Создание сопоставлений таблиц

 Так как используется формат данных `json`, сопоставление данных обязательно. Сопоставление `json` сопоставляет каждый путь JSON с именем столбца в таблице.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Метрики диагностики / Журналы диагностики](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Сопоставление метрик и журналов диагностики с таблицей

Чтобы сопоставить данные журналов и метрик диагностики с таблицей, выполните следующий запрос:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","Properties":{"path":"$.records"}}]'
```

# <a name="activity-logs"></a>[Журналы действий](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Сопоставление журналов действий с таблицей

Чтобы сопоставить данные журнала действий с таблицей, выполните следующий запрос:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","Properties":{"path":"$.records"}}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Создание политики обновления для данных журнала и метрик

# <a name="diagnostic-metrics"></a>[Метрики диагностики](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Создание политики обновления данных для метрик диагностики

1. Создайте [функцию](kusto/management/functions.md), которая развертывает коллекцию записей метрик диагностики таким образом, чтобы каждое значение в коллекции получало отдельную строку. Используйте оператор [`mv-expand`](kusto/query/mvexpandoperator.md):
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Добавьте [политику обновления](kusto/management/updatepolicy.md) для целевой таблицы. Эта политика будет автоматически выполнять запрос для новых данных, принятых в промежуточную таблицу данных *DiagnosticRawRecords* , и вставлять результаты запроса в таблицу *DiagnosticMetrics* :

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Журналы диагностики](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Создание политики обновления данных для журналов диагностики

1. Создайте [функцию](kusto/management/functions.md), которая развертывает коллекцию записей журналов диагностики таким образом, чтобы каждое значение в коллекции получало отдельную строку. Журналы приема можно включить в кластере Azure Data Explorer и использовать [схему журналов приема](using-diagnostic-logs.md#diagnostic-logs-schema). Вы создадите одну таблицу для успешной и одну для неудачной операции приема, а некоторые поля будут пустыми для успешной операции приема (например, ErrorCode). Используйте оператор [`mv-expand`](kusto/query/mvexpandoperator.md):

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Добавьте [политику обновления](kusto/management/updatepolicy.md) для целевой таблицы. Эта политика будет автоматически выполнять запрос для новых данных, принятых в промежуточную таблицу данных *DiagnosticRawRecords* , и вставлять результаты запроса в таблицу *DiagnosticLogs* :

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Журналы действий](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Создание политики обновления данных для журналов действий

1. Создайте [функцию](kusto/management/functions.md), которая развертывает коллекцию записей журнала действий таким образом, чтобы каждое значение в коллекции получало отдельную строку. Используйте оператор [`mv-expand`](kusto/query/mvexpandoperator.md):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Добавьте [политику обновления](kusto/management/updatepolicy.md) для целевой таблицы. Эта политика будет автоматически выполнять запрос для новых данных, принятых в промежуточную таблицу данных *ActivityLogsRawRecords* , и вставлять результаты запроса в таблицу *ActivityLogs* :

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Создание пространства имен Центров событий Azure

Параметры диагностики Azure позволяют экспортировать метрики и журналы в учетную запись хранения или в концентратор событий. В этом учебнике мы направим метрики и журналы в концентратор событий. На следующих шагах вы создадите пространство имен Центров событий и концентратор событий для журналов и метрик диагностики. Служба Azure Monitor создаст концентратор событий *insights-operational-logs* для журналов действий.

1. Создайте концентратор событий на портале Azure с помощью шаблона Azure Resource Manager. Чтобы выполнить остальные шаги из этой статьи, щелкните правой кнопкой кнопку **Развеhнуть в Azure** и выберите вариант **Открыть в новом окне** . Нажав кнопку **Развернуть в Azure** , вы перейдете на портал Azure.

    [![Кнопка "Развертывание в Azure"](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Создайте пространство имен концентраторов событий и концентратор событий для журналов диагностики.

    ![Создание концентратора событий](media/ingest-data-no-code/event-hub.png)

1. Заполните форму, указав следующую информацию. Сохраните значения по умолчанию для всех параметров, не указанных в следующей таблице.

    **Параметр** | **Рекомендуемое значение** | **Описание**
    |---|---|---|
    | **Подписка** | *Ваша подписка* | Выберите подписку Azure, которую нужно использовать для своего концентратора событий.|
    | **Группа ресурсов** | *test-resource-group* | Создайте новую группу ресурсов. |
    | **Расположение** | Выберите регион в соответствии со своими потребностями. | Создайте пространство имен концентратора событий в том же расположении, в котором находятся другие ресурсы.
    | **Имя пространства имен** | *AzureMonitoringData* | Выберите уникальное имя, идентифицирующее пространство имен.
    | **Имя концентратора событий** | *DiagnosticData* | Концентратор событий находится в пространстве имен, предоставляющем уникальный контейнер области. |
    | **Имя группы потребителей** | *adxpipeline* | Создайте имя группы потребителей. Группы получателей событий позволяют каждому из нескольких получающих события приложений иметь отдельное представление потока событий. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Подключение журналов и метрик Azure Monitor к концентратору событий

Теперь вам следует подключить к концентратору событий журналы и метрики диагностики, а также журналы действий.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Метрики диагностики / Журналы диагностики](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Подключение журналов и метрик диагностики к концентратору событий

Выберите ресурс, из которого следует экспортировать метрики. Экспорт данных диагностики поддерживают ресурсы нескольких типов, в том числе пространство имен Центров событий, Azure Key Vault, Центр Интернета вещей и кластер Azure Data Explorer. В этом учебнике в качестве ресурса мы используем кластер Azure Data Explorer, мы рассмотрим метрики производительности запросов и журналы результатов приема.

1. Выберите свой кластер Kusto на портале Azure.
1. Выберите элемент **Параметры диагностики** , а затем ссылку **Включить диагностику** . 

    ![Параметры диагностики](media/ingest-data-no-code/diagnostic-settings.png)

1. Откроется панель **Параметры диагностики** . Сделайте следующее:
   1. Присвойте данным журнала диагностики имя *ADXExportedData* .
   1. В поле **LOG** установите флажки **SucceededIngestion** и **FailedIngestion** .
   1. В разделе **Метрика** установите флажок **Производительность запроса** .
   1. Установите флажок **Передать в концентратор событий** .
   1. Нажмите кнопку **Настроить** .

      ![Панель параметров диагностики](media/ingest-data-no-code/diagnostic-settings-window.png)

1. На панели **Выбор концентратора событий** настройте метод экспорта данных из журналов диагностики в созданный вами концентратор событий.
    1. В списке **Выбрать пространство имен концентратора событий** выберите *AzureMonitoringData* .
    1. В списке **Выберите имя концентратора событий** выберите *DiagnosticData* .
    1. В списке **Выбрать имя политики концентратора событий** выберите **RootManagerSharedAccessKey** .
    1. Щелкните **ОК** .

1. Щелкните **Сохранить** .

# <a name="activity-logs"></a>[Журналы действий](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Подключение журналов действий к концентратору событий

1. В меню слева на портале Azure выберите **Журнал действий** .
1. Откроется окно **Журнал действий** . Выберите **Параметры диагностики** .

    ![Окно "Журнал действий"](media/ingest-data-no-code/activity-log.png)

1. Откроется окно **Параметры диагностики** . Щелкните команду **Добавить параметр диагностики** .

    :::image type="content" source="media/ingest-data-no-code/add-diagnosting-setting.png" alt-text="Элемент &quot;Добавить параметр диагностики&quot; в окне &quot;Параметры диагностики&quot; на портале Azure Data Explorer":::

1. Откроется новое окно **Параметры диагностики** . 

    :::image type="content" source="media/ingest-data-no-code/export-activity-log.PNG" alt-text="Элемент &quot;Добавить параметр диагностики&quot; в окне &quot;Параметры диагностики&quot; на портале Azure Data Explorer":::

    Выполните следующие шаги:
    1. Введите имя в поле **Имя параметра диагностики** .  
    1. В левой части полей с флажками выберите журналы платформы, которые нужно получить из подписки.
    1. Установите флажок **Передать в концентратор событий** .
    1. Выберите свою подписку.
    1. В списке **Пространство имен концентратора событий** выберите элемент *AzureMonitoringData* .
    1. При необходимости выберите нужное **имя концентратора событий** .
    1. В списке **Имя политики концентратора событий** выберите имя политики концентратора событий по умолчанию.
    1. В левом верхнем углу этого окна выберите **Сохранить** . Будет создан концентратор событий с именем *insights-operational-logs* (если не выбрано имя концентратора событий, как указано выше).
      
    

---

### <a name="see-data-flowing-to-your-event-hubs"></a>Просмотр потока данных к концентраторам событий

1. Подождите несколько минут, пока создается подключение и завершается экспорт журнала действий в концентратор событий. Перейдите к пространству имен концентраторов событий, чтобы просмотреть созданные концентраторы событий.

    ![Созданные концентраторы событий](media/ingest-data-no-code/event-hubs-created.png)

1. Просмотрите потоки данных к вашему концентратору событий:

    ![Данные концентратора событий](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Подключение концентратора событий к Azure Data Explorer

Теперь пора создать подключения к данным для журналов и метрик диагностики, а также журналов действий.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Создание подключения к данным для журналов и метрик диагностики, а также журналов действий

1. В кластере Azure Data Explorer с именем *kustodocs* выберите в меню слева **Базы данных** .
1. В окне **Базы данных** выберите базу данных *TestDatabase* .
1. В меню слева выберите **Прием данных** .
1. В окне **Прием данных** щелкните **+Добавить подключение к данным** .
1. В окне **Подключение к данным** введите следующую информацию:

    ![Подключение к данным концентратора событий](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Метрики диагностики / Журналы диагностики](#tab/diagnostic-metrics+diagnostic-logs) 

1. Примените следующие параметры в окне **Подключение к данным** .

    Источник данных:

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Имя подключения к данным** | *DiagnosticsLogsConnection* | Имя создаваемого подключения к обозревателю данных Azure.|
    | **Пространство имен концентратора событий** | *AzureMonitoringData* | Имя, выбранное ранее и определяющее пространство имен. |
    | **Концентратор событий** | *DiagnosticData* | Созданный концентратор событий. |
    | **Группа потребителей** | *adxpipeline* | Группа получателей событий, определенная в созданном концентраторе событий. |
    | | |

    Целевая таблица

    Существует два варианта маршрутизации: *статическая* и *динамическая* . В этом руководстве используется статическая маршрутизация (по умолчанию), для которой нужно указать имя таблицы, формат данных и сопоставление. Не активируйте флажок **My data includes routing info** (Мои данные содержат сведения о маршрутизации).

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Таблица** | *DiagnosticRawRecords* | Таблица, которую вы создали в базе данных *TestDatabase* . |
    | **Формат данных** | *JSON* | Формат, используемый в этой таблице. |
    | **Сопоставление столбцов** | *DiagnosticRawRecordsMapping* | Сопоставление, которое вы создали в базе данных *TestDatabase* между входящими данными JSON и именами столбцов и типами данных в таблице *DiagnosticRawRecords* .|
    | | |

1. Нажмите кнопку **создания** .  

# <a name="activity-logs"></a>[Журналы действий](#tab/activity-logs)

1. Примените следующие параметры в окне **Подключение к данным** .

    Источник данных:

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Имя подключения к данным** | *ActivityLogsConnection* | Имя создаваемого подключения к обозревателю данных Azure.|
    | **Пространство имен концентратора событий** | *AzureMonitoringData* | Имя, выбранное ранее и определяющее пространство имен. |
    | **Концентратор событий** | *insights-operational-logs* | Созданный концентратор событий. |
    | **Группа потребителей** | *$Default* | Группа потребителей по умолчанию. При необходимости вы можете создать другую группу потребителей. |
    | | |

    Целевая таблица

    Существует два варианта маршрутизации: *статическая* и *динамическая* . В этом руководстве используется статическая маршрутизация (по умолчанию), для которой нужно указать имя таблицы, формат данных и сопоставление. Не активируйте флажок **My data includes routing info** (Мои данные содержат сведения о маршрутизации).

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Таблица** | *ActivityLogsRawRecords* | Таблица, которую вы создали в базе данных *TestDatabase* . |
    | **Формат данных** | *JSON* | Формат, используемый в этой таблице. |
    | **Сопоставление столбцов** | *ActivityLogsRawRecordsMapping* | Сопоставление, которое вы создали в базе данных *TestDatabase* между входящими данными JSON и именами столбцов и типами данных в таблице *ActivityLogsRawRecords* .|
    | | |

1. Нажмите кнопку **создания** .  
---

## <a name="query-the-new-tables"></a>Отправка запросов новым таблицам

Теперь у вас есть конвейер, передающий данные. Прием данных через кластер занимает по умолчанию около пяти минут, поэтому подождите несколько минут с момента начала передачи данных, прежде чем начинать отправку запросов.

# <a name="diagnostic-metrics"></a>[Метрики диагностики](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Запрос таблицы метрик диагностики

Приведенный ниже запрос анализирует данные о длительности запросов по записям метрик диагностики в Azure Data Explorer.

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Результаты запроса:

| avg_Average |
| --- |
| 00:06.156 |

# <a name="diagnostic-logs"></a>[Журналы диагностики](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Запрос таблицы журналов диагностики

Этот конвейер производит прием с помощью концентратора событий. Вы просмотрите результаты этого приема.
Приведенный ниже запрос анализирует количество приемов за минуту, включая пример `Database`, `Table` и `IngestionSourcePath` для каждого интервала:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Результаты запроса:

| count_ | any_Database | any_Table | any_IngestionSourcePath |
| --- | --- | --- | --- |
| 00:06.156 | TestDatabase | DiagnosticRawRecords | `https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip` |

# <a name="activity-logs"></a>[Журналы действий](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Запрос таблицы журналов действий

Приведенный ниже запрос анализирует данные в записях журнала действий Azure Data Explorer.

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Результаты запроса:

| avg(DurationMs) |
| --- |
| 768.333 |

---

## <a name="next-steps"></a>Дальнейшие шаги

* Чтобы узнать, как создавать многие другие запросы к данным, которые вы извлекли из Azure Data Explorer, изучите статью [Write queries for Azure Data Explorer](write-queries.md) (Написание запросов для Azure Data Explorer).
* [Monitor Azure Data Explorer ingestion operations using diagnostic logs (Preview)](using-diagnostic-logs.md) (Мониторинг операций приема Azure Data Explorer с помощью журналов диагностики (предварительная версия))
* [Использование метрик для мониторинга работоспособности кластера](using-metrics.md)
