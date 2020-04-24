---
title: Данные запросов в Azure Monitor с помощью Azure Data Explorer (Предварительный просмотр)
description: В этой теме запрашивайте данные в Azure Monitor, создавая прокси-сервер Azure Data Explorer для перекрестных запросов с помощью application Insights и Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 983a9af42772209df2f48c1b1480e8ff0f34b5d6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501390"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Данные запросов в Azure Monitor с помощью Azure Data Explorer (Предварительный просмотр)

Прокси-кластер Azure Data Explorer (ADX Proxy) — это сущность, которая позволяет выполнять перекрестные запросы продукта между Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)и [журналом Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) в службе [Azure Monitor.](/azure/azure-monitor/) Рабочие места анализа журналов Azure Monitor Analytics или приложения Application Insights можно сопоставить с прокси-кластерами. Затем можно задать запрос кластера прокси с помощью инструментов Azure Data Explorer и сослаться на него в запросе перекрестного кластера. В статье показано, как подключиться к кластеру прокси, добавить прокси-кластер в web-uI Azure Data Explorer и запустить запросы в отношении ваших приложений ИИ или рабочих областей La от Azure Data Explorer.

Прокси-поток Azure Data Explorer: 

![Поток прокси ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Предварительные требования

> [!NOTE]
> Прокси ADX находится в режиме предварительного просмотра. [Подключитесь к прокси-серверу,](#connect-to-the-proxy) чтобы включить функцию прокси ADX для кластеров. Свяжитесь с командой [ADXProxy](mailto:adxproxy@microsoft.com) с любыми вопросами.

## <a name="connect-to-the-proxy"></a>Подключение к прокси

1. Проверка натимного кластера Azure Data Explorer (например, кластер *справки)* отображается в левом меню перед подключением к кластеру Log Analytics или Application Insights.

    ![Родной кластер ADX](media/adx-proxy/web-ui-help-cluster.png)

1. В ui Explorer данныхhttps://dataexplorer.azure.com/clusters)Azure ( , выберите **Добавить кластер**.

1. В окне **добавить кластера** добавьте URL-адрес в кластер LA или AI. 
    
    * Для Лос-Анджелеса:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Для ИИ:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Выберите **Добавить**.

    ![Добавление кластера](media/adx-proxy/add-cluster.png)

    Если вы добавляете соединение к более чем одному кластеру прокси, дайте каждому другое имя. В противном случае все они будут иметь то же имя в левом стеку.

1. После установления соединения кластер LA или AI появится в левом стене с вашим родным кластером ADX. 

    ![Кластеры журналов Analytics и Azure Data Explorer](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Выполнение запросов

Вы можете запускать запросы с помощью клиентских инструментов, поддерживающих запросы Kusto, таких как: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, Power'ury, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * Имя базы данных должно иметь то же имя, что и ресурс, указанный в кластере прокси. Имена чувствительны к делу.
> * В запросах поперечному кластеру убедитесь, что наименование приложений Application Insights и рабочих областей Log Analytics является правильным.
>     * Если имена содержат специальные символы, они заменяются кодированием URL в названии кластера прокси. 
>     * Если имена включают символы, которые не соответствуют [правилам идентификатора КЗЛ,](kusto/query/schema-entities/entity-names.md)они заменяются символом dash. **-**

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Прямой запрос из кластера прокси-серверов LA или AI ADX

Запуск запросов в кластере LA или AI. Убедитесь, что кластер выбран в левом стеле. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Рабочее пространство запроса LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Перекрестный запрос вашего кластера Прокси LA или AI ADX и родного кластера ADX 

При запуске перекрестных кластерных запросов из прокси-сервера убедитесь, что родной кластер ADX выбран в левом стеле. Следующие примеры демонстрируют сочетание таблиц `union`кластеров ADX (с использованием) с рабочим пространством LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Перекрестный запрос из прокси-сервера Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Использование [ `join` оператора](kusto/query/joinoperator.md)вместо объединения может [`hint`](kusto/query/joinoperator.md#join-hints) потребоваться для запуска его в родном кластере Azure Data Explorer (а не в прокси-сервере). 

## <a name="additional-syntax-examples"></a>Дополнительные примеры синтаксиса

Следующие варианты синтаксиса доступны при вызове кластеров Application Insights (AI) или Log Analytics (LA):

|Описание синтаксиса  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| База данных в кластере, содержащая только определенный ресурс в этой подписке **(рекомендуется для запросов перекрестного кластера)** |   кластера()`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` | кластера()`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`     |
| Кластер, содержащий все приложения/рабочие области в этой подписке    |     кластера()`https://ade.applicationinsights.io/subscriptions/<subscription-id>`    |    кластера()`https://ade.loganalytics.io/subscriptions/<subscription-id>`     |
|Кластер, содержащий все приложения/рабочие области в подписке и войдём в эту группу ресурсов    |   кластера()`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`      |    кластера()`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`      |
|Кластер, содержащий только определенный ресурс в этой подписке      |    кластера()`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`    |  кластера()`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`     |

## <a name="next-steps"></a>Следующие шаги

[Написание запросов](write-queries.md)
