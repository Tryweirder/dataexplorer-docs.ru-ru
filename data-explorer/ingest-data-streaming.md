---
title: Настройка приема потоковой передачи в кластере Azure обозреватель данных с помощью портал Azure
description: Узнайте, как настроить кластер Azure обозреватель данных и начать загрузку данных с использованием потокового приема с помощью портал Azure.
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7e5d836e25916a039a4df8e451d16e4f8a41cf18
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423247"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-the-azure-portal"></a>Настройка приема потоковой передачи в кластере Azure обозреватель данных с помощью портал Azure

> [!div class="op_single_selector"]
> * [Портал](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера Azure обозреватель данных и базы данных](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Включение приема потоковой передачи в кластере

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster-in-the-azure-portal"></a>Включение приема потоковой передачи при создании нового кластера в портал Azure

Вы можете включить прием потоковой передачи при создании нового кластера Azure обозреватель данных. 

На вкладке **конфигурации** выберите прием **потоковой передачи**  >  **в**.

:::image type="content" source="media/ingest-data-streaming/cluster-creation-enable-streaming.png" alt-text="Включение приема потоковой передачи при создании кластера в Azure обозреватель данных":::

### <a name="enable-streaming-ingestion-on-an-existing-cluster-in-the-azure-portal"></a>Включение приема потоковой передачи в существующем кластере в портал Azure

1. На портале Azure перейдите к кластеру Azure Data Explorer. 
1. В окне **Параметры**выберите **конфигурации**. 
1. В области **конфигурации** выберите **вкл** ., чтобы включить прием **потоковой передачи**.
1. Щелкните **Сохранить**.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-on.png" alt-text="Включение приема потоковой передачи в Azure обозреватель данных":::

> [!WARNING]
> Перед включением приема потоковой передаче ознакомьтесь с [ограничениями](#limitations) .

## <a name="create-a-target-table-and-define-the-policy-in-the-azure-portal"></a>Создайте целевую таблицу и определите политику в портал Azure

1. В портал Azure перейдите к кластеру.
1. Выберите **Запрос**.

    :::image type="content" source="media/ingest-data-streaming/cluster-select-query-tab.png" alt-text="Выберите запрос на портале обозреватель данных Azure для включения приема потоковой передачи":::

1. Чтобы создать таблицу, которая будет получать данные посредством потокового приема, скопируйте следующую команду в **область запроса** и выберите **выполнить**.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    :::image type="content" source="media/ingest-data-streaming/create-table.png" alt-text="Создание таблицы для приема потоковой передачи в Azure обозреватель данных":::

1. Определите [политику приема потоковой передачи](kusto/management/streamingingestionpolicy.md) для созданной таблицы или базы данных, содержащей эту таблицу. 
 
    > [!TIP]
    > Политика, определенная на уровне базы данных, применяется ко всем существующим и будущим таблицам в базе данных. 
    
1. Скопируйте одну из следующих команд на **панель запросов** и выберите **выполнить**.

    ```kusto
    .alter table TestTable policy streamingingestion enable
    ```

    или

    ```kusto
    .alter database StreamingTestDb policy streamingingestion enable
    ```

    :::image type="content" source="media/ingest-data-streaming/define-streaming-ingestion-policy.png" alt-text="Определение политики приема потоковой передачи в Azure обозреватель данных":::

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

## <a name="drop-the-streaming-ingestion-policy-in-the-azure-portal"></a>Удалите политику приема потоковой передачи в портал Azure

1. В портал Azure перейдите к кластеру Azure обозреватель данных и выберите **запрос**. 
1. Чтобы удалить политику приема потоковой передачи из таблицы, скопируйте следующую команду в **область запросов** и выберите **выполнить**.

    ```Kusto
    .delete table TestTable policy streamingingestion 
    ```

    :::image type="content" source="media/ingest-data-streaming/delete-streaming-ingestion-policy.png" alt-text="Удаление политики приема потоковой передачи в Azure обозреватель данных":::

1. В окне **Параметры**выберите **конфигурации**.
1. В области **конфигурации** выберите **выкл** ., чтобы отключить прием **потоковой передачи**.
1. Щелкните **Сохранить**.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-off.png" alt-text="Отключение приема потоковой передачи в Azure обозреватель данных":::

[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Запрос данных в обозреватель данных Azure](web-query-data.md)
