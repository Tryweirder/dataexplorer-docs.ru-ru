---
title: Запрос данных в Azure Monitor с помощью Azure Data Explorer (предварительная версия)
description: В этом разделе описано, как запрашивать данные в Azure Monitor путем создания прокси-сервера в Azure Data Explorer для выполнения запросов между продуктами с помощью Application Insights и Log Analytics.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/28/2020
ms.localizationpriority: high
ms.openlocfilehash: cd0bc28a2d2b282c50a85c87dbf8f4989c7b4057
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513222"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Запрос данных в Azure Monitor с помощью Azure Data Explorer (предварительная версия)

Кластер прокси-сервера Azure Data Explorer (прокси-сервер ADX) — это сущность, позволяющая вам выполнять запросы между продуктами в Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) и [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) в службе [Azure Monitor](/azure/azure-monitor/). Вы можете сопоставлять рабочие области Log Analytics в Azure Monitor или приложения Application Insights в качестве кластеров прокси-серверов. Затем вы можете запрашивать кластер прокси-сервера с помощью средств Azure Data Explorer и ссылаться на него в межкластерном запросе. В этой статье демонстрируется, как подключиться к кластеру прокси-сервера, добавить его в пользовательский веб-интерфейс Azure Data Explorer и выполнять запросы к приложениям AI или рабочим областям LA из Azure Data Explorer.

Поток прокси-сервера в Azure Data Explorer:

![Поток прокси-сервера ADX](media/adx-proxy/adx-proxy-workflow.png)

## <a name="prerequisites"></a>Предварительные требования

> [!NOTE]
> Прокси-сервер ADX предоставляется в предварительной версии. [Подключитесь к прокси-серверу](#connect-to-the-proxy), чтобы включить функции прокси-сервера ADX для своих кластеров. Любые вопросы направляйте группе разработчиков [ADXProxy](mailto:adxproxy@microsoft.com).

## <a name="connect-to-the-proxy"></a>Подключение к прокси-серверу

1. Прежде чем подключиться к кластеру Log Analytics или Application Insights, убедитесь, что в меню слева отображается собственный кластер Azure Data Explorer (например, кластер *help*).

    ![Собственный кластер ADX](media/adx-proxy/web-ui-help-cluster.png)

1. В пользовательском интерфейсе Azure Data Explorer (https://dataexplorer.azure.com/clusters) ) выберите **Добавить кластер**.

1. В окне **Добавление кластера** добавьте URL-адрес кластера LA или AI. 
    
    * Для LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Для AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Выберите **Добавить**.

    ![Добавление кластера](media/adx-proxy/add-cluster.png)

    >[!NOTE]
    >При добавлении подключения к нескольким кластерам прокси-серверов присвойте им разные имена. В противном случае в левой области они будут отображаться с одним именем.

1. После установления подключения в левой области отобразится кластер LA или AI с собственным кластером ADX. 

    ![Кластеры Log Analytics и Azure Data Explorer](media/adx-proxy/la-adx-clusters.png)

> [!NOTE]
> Количество рабочих областей Azure Monitor, которые могут быть сопоставлены, ограничено до 100.

## <a name="run-queries"></a>Выполнение запросов

Запросы можно выполнять с помощью клиентских средств, поддерживающих запросы Kusto, например следующих: Kusto Explorer, пользовательский веб-интерфейс ADX, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!NOTE]
> Функция прокси-сервера ADX используется только для получения данных. Дополнительные сведения см. в разделе [Поддержка функций](#function-supportability).

> [!TIP]
> * Имя базы данных должно совпадать с именем ресурса, указанного в кластере прокси-сервера. В именах учитывается регистр.
> * При использовании межкластерных запросов убедитесь в правильности именования приложений Application Insights и рабочих областей Log Analytics.
> * Если имена содержат специальные символы, эти символы будут заменены кодировкой URL-адресов в имени кластера прокси-сервера. 
> * Если имена содержат символы, которые не соответствуют [правилам для именования идентификаторов KQL](kusto/query/schema-entities/entity-names.md), они заменяются символом тире **-** .

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Прямой запрос из кластера прокси-сервера ADX в LA или AI

Вы можете выполнять запросы на своем кластере LA или AI. Убедитесь, что кластер выбран в левой области. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Отправка запросов к рабочей области LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Перекрестное запрашивание кластера прокси-сервера ADX в LA или AI и собственного кластера ADX

При выполнении запросов между кластерами с прокси-сервера убедитесь, что в левой области выбран собственный кластер ADX. В следующих примерах демонстрируется объединение таблиц кластера ADX (с помощью `union`) с рабочей областью LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Перекрестное запрашивание с прокси-сервера Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Использование [оператора `join`](kusto/query/joinoperator.md) вместо union может потребовать наличия [`hint`](kusto/query/joinoperator.md#join-hints) для его выполнения в собственном кластере Azure Data Explorer (а не на прокси-сервере). 

### <a name="join-data-from-an-adx-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Соединение данных из кластера ADX в одном арендаторе с ресурсом Azure Monitor в другом

Запросы между арендаторами не поддерживаются прокси-сервером ADX. Вы выполняете вход в один арендатор для выполнения запроса с двумя ресурсами.

Если ресурс Azure Data Explorer находится в арендаторе A, а рабочая область LA находится в арендаторе B, используйте один из следующих двух методов:

1. Azure Data Explorer позволяет добавлять роли для субъектов в разные арендаторы. Добавьте свой идентификатор пользователя в арендаторе B в качестве полномочного пользователя в кластере Azure Data Explorer. Убедитесь, что свойство *[TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster)* в кластере Azure Data Explorer содержит арендатор B. Выполните полный перекрестный запрос в арендаторе B.

2. Используйте [Lighthouse](/azure/lighthouse/) для проецирования ресурса Azure Monitor в арендатор A.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Подключение к кластерам Azure Data Explorer из разных арендаторов

Kusto Explorer автоматически выполняет за вас вход в арендатор, которому изначально принадлежит учетная запись пользователя. Чтобы получить доступ к ресурсам в других арендаторах с помощью одной учетной записи пользователя, для `tenantId` необходимо явно указать в строке подключения следующее: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Поддержка функций

Кластер прокси-сервера Azure Data Explorer поддерживает функции как для Application Insights, так и для Log Analytics.
Эта возможность позволяет выполнять межкластерные запросы для указания прямых ссылок на табличную функцию Azure Monitor.
Прокси-сервер поддерживает следующие команды:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

На следующем рисунке показан пример запрашивания табличной функции из пользовательского веб-интерфейса Azure Data Explorer.
Чтобы использовать функцию, запустите ее по имени в окне "Запрос".

  [ ![Запрашивание табличной функции из пользовательского веб-интерфейса Azure Data Explorer](media/adx-proxy/function-query-adx-proxy.png)](media/adx-proxy/function-query-adx-proxy.png#lightbox)


## <a name="additional-syntax-examples"></a>Дополнительные примеры синтаксиса

При вызове кластеров Application Insights (AI) или Log Analytics (LA) доступны следующие параметры синтаксиса:

|Описание синтаксиса  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| База данных в кластере, которая содержит только определенный ресурс в этой подписке (**рекомендуется для межкластерных запросов**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Кластер, содержащий все приложения и рабочие области в этой подписке    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Кластер, содержащий все приложения и рабочие области в подписке, которые входят в эту группу ресурсов    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Кластер, содержащий только определенный ресурс в этой подписке      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов](write-queries.md)