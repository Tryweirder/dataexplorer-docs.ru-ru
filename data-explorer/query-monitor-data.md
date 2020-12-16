---
title: Запрос данных в Azure Monitor с помощью Azure Data Explorer (предварительная версия)
description: В этой статье описано, как запрашивать данные в Azure Monitor (Application Insights и Log Analytics) путем создания запросов между продуктами в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 607bcc7b1c0401116a94a9c06ff308a06ad936b7
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371583"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Запрос данных в Azure Monitor с помощью Azure Data Explorer (предварительная версия)

Azure Data Explorer поддерживает запросы между службами Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) и [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs). Вы можете отправлять запросы к своей рабочей области Log Analytics или Application Insights с помощью инструментов Azure Data Explorer для работы с запросами и в запросах между службами. В этой статье описано, как создать запрос между службами и добавить рабочую область Log Analytics или Application Insights в веб-интерфейс Azure Data Explorer.

Поток запросов между службами в Azure Data Explorer:

![Поток с использованием прокси-сервера в Azure Data Explorer](media/query-monitor-data/query-monitor-workflow.png)

> [!NOTE]
> * Возможность запрашивать данные Azure Monitor из Azure Data Explorer (напрямую через клиентские средства Azure Data Explorer или опосредованно путем выполнения запроса в кластере Azure Data Explorer) предоставляется в предварительной версии.
>* За помощью обращайтесь к [команде по запросам между службами](mailto:adxproxy@microsoft.com).


## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Добавление рабочей области Log Analytics/Application Insights в клиентские средства Azure Data Explorer

Чтобы включить запросы между службами для своих кластеров, добавьте рабочую область Log Analytics или Application Insights в клиентские средства Azure Data Explorer.

1. Прежде чем подключиться к кластеру Log Analytics или Application Insights, убедитесь, что в меню слева отображается собственный кластер Azure Data Explorer (например, кластер *help*).

    ![Собственный кластер Azure Data Explorer](media/query-monitor-data/web-ui-help-cluster.png)

1. В пользовательском интерфейсе Azure Data Explorer (https://dataexplorer.azure.com/clusters) ) выберите **Добавить кластер**.

1. В окне **Добавление кластера** добавьте URL-адрес кластера LA или AI.

    * Для LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Для AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

1. Выберите **Добавить**.

    ![Добавление кластера](media/query-monitor-data/add-cluster.png)

    >[!TIP]
    >Если вы добавляете подключение к более чем одной рабочей области Log Analytics или Application Insights, присвойте ему в каждом случае отдельное имя. В противном случае в левой области они будут отображаться с одним именем.

1. Когда подключение будет установлено, рабочая область Log Analytics или Application Insights отобразится на панели слева с вашим собственным кластером Azure Data Explorer.

    ![Кластеры Log Analytics и Azure Data Explorer](media/query-monitor-data/la-adx-clusters.png)

> [!NOTE]
> Количество рабочих областей Azure Monitor, которые могут быть сопоставлены, ограничено до 100.

## <a name="run-queries"></a>Выполнение запросов

Запросы можно выполнять с помощью клиентских средств, поддерживающих запросы Kusto, например следующих: Kusto Explorer, веб-интерфейс Azure Data Explorer, Jupyter Kqlmagic, Flow, Power Query, PowerShell, Lens, REST API.

> [!NOTE]
> Возможность отправки запросов между службами используется только для получения данных. Дополнительные сведения см. в разделе [Поддержка функций](#function-supportability).

> [!TIP]
> * Имя базы данных должно совпадать с именем ресурса, указанного в запросе между службами. В именах учитывается регистр.
> * При использовании запросов между службами убедитесь, что имена приложений Application Insights и рабочих областей Log Analytics указаны правильно.
> * Если имена содержат специальные знаки, в запросе между службами они будут заменены кодировкой URL-адресов.
> * Если имена содержат символы, которые не соответствуют [правилам для именования идентификаторов KQL](kusto/query/schema-entities/entity-names.md), они заменяются символом тире **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Отправка прямых запросов к рабочим областям Log Analytics или Application Insights из клиентских средств Azure Data Explorer

Вы можете выполнять запросы к рабочим областям Log Analytics или Application Insights из клиентских средств Azure Data Explorer. 

1. Убедитесь, что рабочая область выбрана на панели слева.

1. Выполните приведенный ниже запрос:

```kusto
Perf | take 10 // Demonstrate cross-service query on the Log Analytics workspace
```

![Отправка запроса к рабочей области Log Analytics](media/query-monitor-data/query-la.png)

### <a name="cross-query-of-your-log-analytics-or-application-insights-workspace-and-the-azure-data-explorer-native-cluster"></a>Отправка запросов между рабочими областями Log Analytics или Application Insights и собственным кластером Azure Data Explorer

При выполнении запросов между кластерами убедитесь, что на панели слева выбран собственный кластер Azure Data Explorer. В следующих примерах демонстрируется объединение таблиц кластера Azure Data Explorer (с помощью `union`) с рабочей областью Log Analytics.

Выполните следующие запросы:

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Запрос между службами из Azure Data Explorer](media/query-monitor-data/cross-query.png)](media/query-monitor-data/cross-query.png#lightbox)

> [!TIP]
> Использование [оператора `join`](kusto/query/joinoperator.md) вместо union может потребовать наличия [`hint`](kusto/query/joinoperator.md#join-hints) для его выполнения в собственном кластере Azure Data Explorer.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Объединение данных из кластера Azure Data Explorer в одном арендаторе с ресурсом Azure Monitor в другом

Межтенантные запросы между службами не поддерживаются. Вы выполняете вход в один арендатор для выполнения запроса с двумя ресурсами.

Если ресурс Azure Data Explorer находится в арендаторе A, а рабочая область Log Analytics — в арендаторе B, используйте один из следующих двух методов:

1. Azure Data Explorer позволяет добавлять роли для субъектов в разные арендаторы. Добавьте свой идентификатор пользователя в арендаторе B в качестве полномочного пользователя в кластере Azure Data Explorer. Убедитесь, что свойство *[TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster)* в кластере Azure Data Explorer содержит арендатор B. Выполните полный перекрестный запрос в арендаторе B.

2. Используйте [Lighthouse](/azure/lighthouse/) для проецирования ресурса Azure Monitor в арендатор A.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Подключение к кластерам Azure Data Explorer из разных арендаторов

Kusto Explorer автоматически выполняет за вас вход в арендатор, которому изначально принадлежит учетная запись пользователя. Чтобы получить доступ к ресурсам в других арендаторах с помощью одной учетной записи пользователя, для `tenantId` необходимо явно указать в строке подключения следующее: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Поддержка функций

Запросы между службами Azure Data Explorer поддерживают функции как для Application Insights, так и для Log Analytics.
Эта возможность позволяет выполнять межкластерные запросы для указания прямых ссылок на табличную функцию Azure Monitor.
Для запросов между службами поддерживаются следующие команды:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

На следующем рисунке показан пример запрашивания табличной функции из пользовательского веб-интерфейса Azure Data Explorer.
Чтобы использовать функцию, запустите ее по имени в окне "Запрос".

  [ ![Запрашивание табличной функции из пользовательского веб-интерфейса Azure Data Explorer](media/query-monitor-data/function-query.png)](media/query-monitor-data/function-query.png#lightbox)

## <a name="additional-syntax-examples"></a>Дополнительные примеры синтаксиса

При вызове кластеров Application Insights или Log Analytics доступны следующие параметры синтаксиса:

|Описание синтаксиса  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| База данных в кластере, которая содержит только определенный ресурс в этой подписке (**рекомендуется для межкластерных запросов**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Кластер, содержащий все приложения и рабочие области в этой подписке    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Кластер, содержащий все приложения и рабочие области в подписке, которые входят в эту группу ресурсов    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Кластер, содержащий только определенный ресурс в этой подписке      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов](write-queries.md)