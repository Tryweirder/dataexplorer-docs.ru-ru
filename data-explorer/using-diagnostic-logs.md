---
title: Monitor Azure Data Explorer ingestion operations using diagnostic logs (Preview) (Мониторинг операций приема Azure Data Explorer с помощью журналов диагностики (предварительная версия))
description: Узнайте, как настроить диагностические журналы для Azure Data Explorer для мониторинга операций по поимке.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: c4c7114dd53b8f77150f88cf3da5d26ca73bb93b
ms.sourcegitcommit: 2baa6a1c25ba25ef02a53b4f8d023ea821bd04ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81527594"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs"></a>Monitor Azure Data Explorer ingestion operations using diagnostic logs (Preview) (Мониторинг операций приема Azure Data Explorer с помощью журналов диагностики (предварительная версия))

Azure Data Explorer — это быстрая и полностью управляемая служба для аналитики большого объема потоковых данных в реальном времени, поступающих из приложений, а также с веб-сайтов, устройств Интернета вещей и т. д. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы глотаете (загружаете) данные в таблицу в базе данных, чтобы можно было запускать запросы против нее. [Диагностические журналы Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) предоставляют данные о работе ресурсов Azure. Azure Data Explorer использует диагностические журналы для получения информации об успехах и сбоях в проглатывании. Для мониторинга состояния входа можно экспортировать журналы операций в Azure Storage, Event Hub или Log Analytics. Для дальнейшего анализа журналы из Azure Storage и Azure Event Hub могут быть направлены в таблицу в кластере Azure Data Explorer.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас нет подписки На Azure, создайте [бесплатную учетную запись Azure.](https://azure.microsoft.com/free/)
* Создание [кластера и базы данных](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Настройка диагностических журналов для кластера Azure Data Explorer

Диагностические журналы могут быть использованы для настройки сбора следующих данных журнала:
* Успешные операции по проглатыванию: В этих журналах есть информация об успешно завершенных операциях по проглатыванию.
* Неудачные операции по проглатыванию: Эти журналы содержат подробную информацию о неудачных операциях по проглатыванию, включая сведения об ошибках. 

Затем данные заархивированы в учетную запись хранения, передаются в концентратор событий или отправляются в журнал Analytics в зависимости от ваших спецификаций.

### <a name="enable-diagnostic-logs"></a>Включение журналов диагностики

По умолчанию журналы диагностики отключены. Для включения диагностических журналов сделайте следующие действия:

1. На [портале Azure](https://portal.azure.com)выберите ресурс кластера Azure Data Explorer, который необходимо контролировать.
1. В разделе **Мониторинг** выберите **Параметры диагностики**.
  
    ![Добавление журналов диагностики](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Выберите **Диагностическую настройку добавления.**
1. В окне **настроек диагностики:**
 
    ![Конфигурация параметров диагностики](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Выберите **название** для диагностической настройки.
    1. Выберите одну или несколько целей: учетную запись хранения, концентратор событий или аналитику журналов.
    1. Выберите журналы для `SucceededIngestion` `FailedIngestion`сбора: или .
    1. Выберите [метрики,](using-metrics.md#supported-azure-data-explorer-metrics) которые необходимо собрать (необязательно).  
    1. Выберите **Сохранить,** чтобы сохранить новые настройки и метрики диагностических журналов.

Новые настройки будут установлены через несколько минут. Затем журналы отображаются в настроенной архивной цели (учетная запись хранения, концентратор событий или аналитика журналов). 

## <a name="diagnostic-logs-schema"></a>Схема журналов диагностики

Все [диагностические журналы Azure Monitor имеют общую схему верхнего уровня.](/azure/azure-monitor/platform/diagnostic-logs-schema) Azure Data Explorer обладает уникальными свойствами для собственных событий. Все журналы хранятся в формате JSON.

### <a name="ingestion-logs-schema"></a>Схема журналов заболоции

Строки журнала JSON включают элементы, перечисленные в следующей таблице:

|Имя               |Описание
|---                |---
|time               |Время отчета
|resourceId         |Идентификатор ресурса Azure Resource Manager
|operationName      |Название операции: "MICROSOFT. KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |Версия схемы: '1.0' 
|категория           |Категория операции. `SucceededIngestion` или `FailedIngestion`. Свойства различаются для [успешной работы](#successful-ingestion-operation-log) или [неудачной операции.](#failed-ingestion-operation-log)
|properties         |Подробная информация об операции.

#### <a name="successful-ingestion-operation-log"></a>Успешный журнал операции по проглатыванию

**Примере:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Свойства успешной работы диагностического журнала**

|Имя               |Описание
|---                |---
|succeededOn        |Время завершения приема
|operationId        |Идентификатор операции по проглатке Azure Data Explorer
|База данных           |Название целевой базы данных
|table              |Название целевой таблицы
|проглатываниеИсточник  |Идентификатор источника данных приема
|проглатываниеИсточникPath|Путь источника данных приема или blob URI
|rootActivityId     |Идентификатор действия

#### <a name="failed-ingestion-operation-log"></a>Неудавшийся журнал операции по проглатыванию

**Примере:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Свойства невыполненной операции диагностического журнала**

|Имя               |Описание
|---                |---
|не удалось           |Время завершения приема
|operationId        |Идентификатор операции по проглатке Azure Data Explorer
|База данных           |Название целевой базы данных
|table              |Название целевой таблицы
|проглатываниеИсточник  |Идентификатор источника данных приема
|проглатываниеИсточникPath|Путь источника данных приема или blob URI
|rootActivityId     |Идентификатор действия
|подробности            |Подробное описание сообщения о сбое и ошибке
|errorCode          |Код ошибки 
|отказСтатус      |`Permanent` или `Transient`. Повторная попытка переходного отказа может увенчаться успехом.
|originatesFromUpdatePolicy|Правда, если сбой исходит из политики обновления
|shouldRetry        |Правда, если повторная попытка может увенчаться успехом

## <a name="next-steps"></a>Следующие шаги

* [Учебник: Данные мониторинга ingest и запроса в Azure Data Explorer](ingest-data-no-code.md)
* [Использование метрик для мониторинга работоспособности кластера](using-metrics.md)

