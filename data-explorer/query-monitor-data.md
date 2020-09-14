---
title: Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)
description: В этом разделе вы запрашиваете данные в Azure Monitor, создавая прокси-сервер обозреватель данных Azure для перекрестных запросов с помощью Application Insights и Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 078737ff7e5cd74d15792cc2f0f058cb3ea12a19
ms.sourcegitcommit: e0cf581d433bbbb2eda5a4209a8eabcdae80c21b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90059484"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Запрос данных в Azure Monitor с помощью обозреватель данных Azure (Предварительная версия)

Кластер Azure обозреватель данных Proxy (прокси-сервер ADX) — это сущность, которая позволяет выполнять перекрестные запросы между Azure обозреватель данных, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)и [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) в службе [Azure Monitor](/azure/azure-monitor/) . Вы можете сопоставлять Azure Monitor Log Analytics рабочие области или приложения Application Insights в качестве кластерных прокси-серверов. Затем можно выполнить запрос к кластеру прокси-сервера с помощью средств обозреватель данных Azure и обратиться к нему в межкластерном запросе. В этой статье показано, как подключиться к кластеру прокси-сервера, добавить прокси-кластер в Azure обозреватель данных Web UI и выполнить запросы к приложениям ии или LA рабочей области из Azure обозреватель данных.

Поток прокси-сервера Azure обозреватель данных: 

![Поток прокси-сервера ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Предварительные требования

> [!NOTE]
> Прокси-сервер ADX находится в режиме предварительного просмотра. [Подключитесь к прокси-](#connect-to-the-proxy) серверу, чтобы включить функцию прокси-сервера ADX для кластеров. Свяжитесь с командой [адкспрокси](mailto:adxproxy@microsoft.com) с любыми вопросами.

## <a name="connect-to-the-proxy"></a>Подключение к прокси-серверу

1. Прежде чем подключиться к кластеру Log Analytics или Application Insights, убедитесь, что в меню слева находится собственный кластер Azure обозреватель данных (например, " *Справочный* кластер)".

    ![ADX собственный кластер](media/adx-proxy/web-ui-help-cluster.png)

1. В пользовательском интерфейсе Azure обозреватель данных ( https://dataexplorer.azure.com/clusters) выберите **Добавить кластер**.

1. В окне **Добавление кластера** добавьте URL-адрес кластера Ла или искусственного интеллекта. 
    
    * Для LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Для искусственного интеллекта: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Выберите **Добавить**.

    ![Добавление кластера](media/adx-proxy/add-cluster.png)

    >[!NOTE]
    >При добавлении подключения к нескольким кластерам прокси-серверов присвойте каждому из них другое имя. В противном случае все они будут иметь одинаковое имя в левой области.

1. После установления подключения на левой панели отобразится кластер LA или AI с собственным кластером ADX. 

    ![Кластеры обозреватель данных Log Analytics и Azure](media/adx-proxy/la-adx-clusters.png)

> [!NOTE]
> Количество Azure Monitor рабочих областей, которые могут быть сопоставлены, ограничено 100.

## <a name="run-queries"></a>Выполнение запросов

Запросы можно выполнять с помощью клиентских средств, поддерживающих запросы Kusto, таких как Kusto Explorer, ADX Web UI, Jupyter Кклмагик, Flow, PowerQuery, PowerShell, Джарвис, линз, REST API.

> [!NOTE]
> Функция прокси-сервера ADX используется только для получения данных. Дополнительные сведения см. в разделе [Поддержка функций](#function-supportability).

> [!TIP]
> * Имя базы данных должно совпадать с именем ресурса, указанного в кластере прокси. В именах учитывается регистр.
> * В запросах между кластерами убедитесь в правильности именования Application Insights приложений и Log Analytics рабочих областей.
> * Если имена содержат специальные символы, они заменяются на кодировку URL-адресов в имени кластера прокси-сервера. 
> * Если имена содержат символы, которые не соответствуют [правилам именования идентификаторов ККЛ](kusto/query/schema-entities/entity-names.md), они заменяются **-** символом дефиса.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Прямой запрос из кластера Ла или ИСКУССТВЕНного прокси-сервера ADX

Выполнение запросов в кластере LA или искусственного интеллекта. Убедитесь, что кластер выбран в левой области. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Запрос LA Workspace](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Перекрестный запрос кластера прокси-сервера LA или ИСКУССТВЕНного ADX и собственного кластера ADX

При выполнении запросов между кластерами от прокси-сервера убедитесь, что на левой панели выбран собственный кластер ADX. В следующих примерах демонстрируется объединение таблиц кластера ADX (с помощью `union` ) с La Workspace.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Перекрестный запрос от прокси-сервера Azure обозреватель данных](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

При использовании [ `join` оператора](kusto/query/joinoperator.md)вместо объединения, может потребоваться, [`hint`](kusto/query/joinoperator.md#join-hints) чтобы запустить его в собственном кластере Azure обозреватель данных (а не на прокси-сервере). 

## <a name="function-supportability"></a>Поддержка функций

Кластер Azure обозреватель данных прокси-сервер поддерживает функции как для Application Insights, так и для Log Analytics.
Эта возможность позволяет запросам между кластерами ссылаться на Azure Monitor табличные функции напрямую.
Прокси-сервер поддерживает следующие команды:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

На следующем рисунке показан пример запроса табличной функции из пользовательского веб-интерфейса Azure обозреватель данных.
Чтобы использовать функцию, запустите ее имя в окне запроса.

  [![Запрос табличной функции из пользовательского веб-интерфейса Azure обозреватель данных](media/adx-proxy/function-query-adx-proxy.png)](media/adx-proxy/function-query-adx-proxy.png#lightbox)


## <a name="additional-syntax-examples"></a>Дополнительные примеры синтаксиса

При вызове кластеров Application Insights (AI) или Log Analytics (LA) доступны следующие параметры синтаксиса:

|Описание синтаксиса  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| База данных в кластере, которая содержит только определенный ресурс в этой подписке (**рекомендуется для запросов между кластерами**) |   кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Кластер, содержащий все приложения и рабочие области в этой подписке    |     кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Кластер, содержащий все приложения и рабочие области в подписке и входящие в эту группу ресурсов    |   кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Кластер, содержащий только определенный ресурс в этой подписке      |    кластер ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  кластер ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов](write-queries.md)
