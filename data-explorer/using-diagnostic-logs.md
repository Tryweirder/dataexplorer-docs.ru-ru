---
title: Мониторинг приема, команд и запросов Azure обозреватель данных с помощью журналов диагностики
description: Узнайте, как настроить журналы диагностики для обозреватель данных Azure, чтобы отслеживать команды приема и операции запросов.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.openlocfilehash: 5dbd1aeb777b067e0c7bee15be838eb2f306086f
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371481"
---
# <a name="monitor-azure-data-explorer-ingestion-commands-and-queries-using-diagnostic-logs"></a>Мониторинг приема, команд и запросов Azure обозреватель данных с помощью журналов диагностики

Azure Data Explorer — это быстрая и полностью управляемая служба для аналитики большого объема потоковых данных в реальном времени, поступающих из приложений, а также с веб-сайтов, устройств Интернета вещей и т. д. [Azure Monitor журналы диагностики](/azure/azure-monitor/platform/diagnostic-logs-overview) содержат данные о работе ресурсов Azure. Azure обозреватель данных использует журналы диагностики для анализа результатов приема, сбоев приема, команд и операций запросов. Вы можете экспортировать журналы операций в службу хранилища Azure, концентратор событий или Log Analytics для отслеживания приема, команд и состояния запросов. Журналы из службы хранилища Azure и концентратора событий Azure можно направлять в таблицу в кластере Azure обозреватель данных для дальнейшего анализа.

> [!IMPORTANT] 
> Данные журнала диагностики могут содержать конфиденциальные данные. Ограничьте разрешения назначения журналов в соответствии с потребностями мониторинга. 

## <a name="prerequisites"></a>Предварительные требования

* Если у вас нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
* Войдите на [портал Azure](https://portal.azure.com/).
* Создайте [кластер и базу данных](create-cluster-database-portal.md).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Настройка журналов диагностики для кластера Azure обозреватель данных

Журналы диагностики можно использовать для настройки сбора следующих данных журнала:

# <a name="ingestion"></a>[Прием](#tab/ingestion)

> [!NOTE]
> Журналы приема поддерживаются для приема в очереди на конечную точку приема с помощью пакетов SDK, подключений к данным и соединителей.
>
> Журналы приема не поддерживаются для приема потоковой передачи, прямого приема для механизма, приема из запроса или команд Set-или-Append.

> [!NOTE]
> Журналы приемки с ошибками отображаются только для конечного состояния операции приема, в отличие от (результат приема) [using-метрики # приемки # — метрики #], которая создается для временных сбоев, которые повторно выполняются внутри.

* **Успешные операции приема**: эти журналы содержат сведения об успешно завершенных операциях приема.
* **Неудачные операции приема**: эти журналы содержат подробные сведения о неудачных операциях приема, включая сведения об ошибке. 
* **Операции пакетной обработки приема**: эти журналы содержат подробную статистику пакетов, готовых к приему (продолжительность, размер пакета и количество больших двоичных объектов).

# <a name="commands-and-queries"></a>[Команды и запросы](#tab/commands-and-queries)

* **Команды**. Эти журналы содержат сведения о командах администрирования, которые достигают конечного состояния.
* **Запросы**. Эти журналы содержат подробные сведения о запросах, которые достигают конечного состояния. 

    > [!NOTE]
    > Данные журнала запросов не содержат текст запроса.

---

Затем данные архивируются в учетную запись хранения, передаются в концентратор событий или отправляются в Log Analytics в соответствии со своими спецификациями.

### <a name="enable-diagnostic-logs"></a>Включение журналов диагностики

По умолчанию журналы диагностики отключены. Чтобы включить журналы диагностики, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите ресурс кластера Azure обозреватель данных, который требуется отслеживать.
1. В разделе **Мониторинг** выберите **Параметры диагностики**.
  
    ![Добавление журналов диагностики](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Выберите **Добавить параметр диагностики**.
1. В окне **параметры диагностики** выполните следующие действия.

    :::image type="content" source="media/using-diagnostic-logs/configure-diagnostics-settings.png" alt-text="Настройка параметров диагностики":::

    1. Введите **имя параметра диагностики**.
    1. Выберите один или несколько целевых объектов: рабочую область Log Analytics, учетную запись хранения или концентратор событий.
    1. Выберите журналы для сбора: `SucceededIngestion` , `FailedIngestion` , `Command` или `Query` , `TableUsageStatistics` или `TableDetails` .
    1. Выберите [метрики](using-metrics.md#supported-azure-data-explorer-metrics) для сбора (необязательно).  
    1. Нажмите кнопку **сохранить** , чтобы сохранить новые параметры и метрики журналов диагностики.

Новые параметры будут установлены через несколько минут. Журналы затем отображаются в настроенном целевом объекте архивации (учетная запись хранения, концентратор событий или Log Analytics). 

> [!NOTE]
> При отправке журналов в log Analytics `SucceededIngestion` журналы,, `FailedIngestion` `Command` и `Query` будут храниться в log Analytics таблицах с именами: `SucceededIngestion` , `FailedIngestion` ,,, `ADXIngestionBatching` `ADXCommand` `ADXQuery` соответственно.

## <a name="diagnostic-logs-schema"></a>Схема журналов диагностики

Все [журналы диагностики Azure Monitor используют общую схему верхнего уровня](/azure/azure-monitor/platform/diagnostic-logs-schema). Обозреватель данных Azure имеет уникальные свойства для собственных событий. Все журналы хранятся в формате JSON.

# <a name="ingestion"></a>[Прием](#tab/ingestion)

### <a name="ingestion-logs-schema"></a>Схема журналов приема

Строки JSON журнала включают элементы, перечисленные в следующей таблице.

|Имя               |Описание
|---                |---
|time               |Время отчета
|resourceId         |Идентификатор ресурса Azure Resource Manager
|operationName      |Имя операции: "MICROSOFT. KUSTO/CLUSTERS/ПРИЕМ/ДЕЙСТВИЕ "
|operationVersion   |Версия схемы: "1,0" 
|категория           |Категория операции. `SucceededIngestion`, `FailedIngestion` или `IngestionBatching`. Свойства различаются для [успешной работы](#successful-ingestion-operation-log), [неудачной операции](#failed-ingestion-operation-log) или [выполнения пакетной обработки](#ingestion-batching-operation-log).
|properties         |Подробные сведения об операции.

#### <a name="successful-ingestion-operation-log"></a>Журнал успешных операций приема

**Пример**.

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "SucceededOn": "2019-05-27 07:55:05.3693628",
        "OperationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "Database": "Samples",
        "Table": "StormEvents",
        "IngestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "IngestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Свойства журнала диагностики успешных операций**

|Имя               |Описание
|---                |---
|сукцеедедон        |Время завершения приема
|Операции        |ИДЕНТИФИКАТОР операции приема обозреватель данных Azure
|База данных           |Имя целевой базы данных
|Таблица              |Имя целевой таблицы
|инжестионсаурцеид  |Идентификатор источника данных приема
|инжестионсаурцепас|Путь к источнику данных приема или URI BLOB-объекта
|RootActivityId     |Идентификатор действия

#### <a name="failed-ingestion-operation-log"></a>Ошибка при приеме журнала операций приема

**Пример**.

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

**Свойства журнала диагностики невыполненной операции**

|Имя               |Описание
|---                |---
|фаиледон           |Время завершения приема
|Операции        |ИДЕНТИФИКАТОР операции приема обозреватель данных Azure
|База данных           |Имя целевой базы данных
|Таблица              |Имя целевой таблицы
|инжестионсаурцеид  |Идентификатор источника данных приема
|инжестионсаурцепас|Путь к источнику данных приема или URI BLOB-объекта
|RootActivityId     |Идентификатор действия
|Сведения            |Подробное описание ошибки и сообщения об ошибке
|ErrorCode          |Код ошибки 
|фаилурестатус      |`Permanent` или `Transient`. Повторная попытка временного сбоя может быть выполнена успешно.
|оригинатесфромупдатеполици|True, если ошибка возникла из политики обновления
|шаулдретри        |Значение true, если повторная попытка может быть выполнена

#### <a name="ingestion-batching-operation-log"></a>Журнал операций пакетной обработки приема

**Пример**.

```json
{
  "resourceId": "/SUBSCRIPTIONS/12534EB3-8109-4D84-83AD-576C0D5E1D06/RESOURCEGROUPS/KEREN/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KERENEUS",
  "time": "2020-05-27T07:55:05.3693628Z",
  "operationVersion": "1.0",
  "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGESTIONBATCHING/ACTION",
  "category": "IngestionBatching",
  "correlationId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735",
  "properties": {
    "Database": "Samples",
    "Table": "StormEvents",
    "BatchingType": "Size",
    "SourceCreationTime": "2020-05-27 07:52:04.9623640",
    "BatchTimeSeconds": 215.5,
    "BatchSizeBytes": 2356425,
    "DataSourcesInBatch": 4,
    "RootActivityId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735"
  }
}

```
**Свойства журнала диагностики операции пакетной обработки приема**

|Имя               |Описание
|---                   |---
| TimeGenerated        | Время (UTC) создания события |
| База данных             | Имя базы данных, содержащей целевую таблицу |
| Таблица                | Имя целевой таблицы, в которую принимаются данные |
| батчингтипе         | Тип пакетной обработки: соответствует ли пакету время пакетной обработки, размер данных или количество файлов, заданное политикой пакетной обработки |
| саурцекреатионтиме   | Минимальное время (в формате UTC) создания больших двоичных объектов в этом пакете |
| батчтимесекондс     | Общее время пакетного выполнения этого пакета (в секундах) |
| батчсизебитес       | Общий размер несжатых данных в этом пакете (в байтах) |
| датасаурцесинбатч   | Число источников данных в этом пакете |
| RootActivityId       | Идентификатор действия операции |


# <a name="commands-and-queries"></a>[Команды и запросы](#tab/commands-and-queries)

### <a name="commands-and-queries-logs-schema"></a>Схема журналов команд и запросов

Строки JSON журнала включают элементы, перечисленные в следующей таблице.

|Имя               |Описание
|---                |---
|time               |Время отчета
|resourceId         |Идентификатор ресурса Azure Resource Manager
|operationName      |Имя операции: "MICROSOFT. KUSTO/CLUSTERs/COMMAND/ACTION ' или ' MICROSOFT. KUSTO/CLUSTERS/ЗАПРОС/ДЕЙСТВИЕ ". Свойства различаются для команд и журналов запросов.
|operationVersion   |Версия схемы: "1,0" 
|категория           |Категория операции: `Command` или `Query` . Свойства различаются для команд и журналов запросов.
|properties         |Подробные сведения об операции.

#### <a name="command-log"></a>Журнал команд

**Пример**.

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION",
    "operationVersion": "1.0",
    "category": "Command",
    "properties":
    {
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d",
        "StartedOn": "2020-09-12T18:06:04.6898353Z",
        "LastUpdatedOn": "2020-09-12T18:06:04.6898353Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "XXX",
        "TotalCpu": "00:01:30.1562500",
        "CommandType": "ExtentsDrop",
        "Application": "Kusto.WinSvc.DM.Svc",
        "ResourceUtilization": "{\"CacheStatistics\":{\"Memory\":{\"Hits\":0,\"Misses\":0},\"Disk\":{\"Hits\":0,\"Misses\":0},\"Shards\":{\"Hot\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"Cold\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"BypassBytes\":0}},\"TotalCpu\":\"00:00:00\",\"MemoryPeak\":0,\"ScannedExtentsStatistics\":{\"MinDataScannedTime\":null,\"MaxDataScannedTime\":null,\"TotalExtentsCount\":0,\"ScannedExtentsCount\":0,\"TotalRowsCount\":0,\"ScannedRowsCount\":0}}",
        "Duration": "00:03:30.1562500",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a3b4136b53;5c443533-c927-4410-a5d6-4d6a5443b64f"
    }
}
```
**Свойства журнала диагностики команд**

|Имя               |Описание
|---                |---
|RootActivityId |ИД корневого действия
|стартедон        |Время (UTC) запуска этой команды
|ластупдатедон        |Время (UTC) окончания выполнения команды
|База данных          |Имя базы данных, в которой выполнялась команда
|Состояние              |Состояние, в котором была завершена команда
|FailureReason  |Причина сбоя
|тоталкпу |Общая длительность ЦП
|CommandType     |Тип команды
|Приложение     |Имя приложения, вызвавшего команду
|ResourceUtilization     |Использование ресурсов команды
|Длительность     |Длительность команды
|Пользователь     |Пользователь, который вызвал запрос
|Основной     |Участник, который вызвал запрос

#### <a name="query-log"></a>Журнал запросов

**Пример**.

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION",
    "operationVersion": "1.0",
    "category": "Query",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-09-04T13:45:45.331925Z",
        "LastUpdatedOn": "2020-09-04T13:45:45.3484372Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "[none]",
        "TotalCpu": "00:00:00",
        "ApplicationName": "MyApp",
        "MemoryPeak": 0,
        "Duration": "00:00:00.0165122",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf",
        "ScannedExtentsStatistics": {
            "MinDataScannedTime": "2020-07-27T08:34:35.3299941",
            "MaxDataScannedTime": "2020-07-27T08:34:41.991661",
            "TotalExtentsCount": 64,
            "ScannedExtentsCount": 64,
            "TotalRowsCount": 320,
            "ScannedRowsCount": 320
        },
        "CacheStatistics": {
            "Memory": {
                "Hits": 192,
                "Misses": 0
          },
            "Disk": {
                "Hits": 0,
                "Misses": 0
      },
            "Shards": {
                "Hot": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
                "Cold": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
            "BypassBytes": 0
      }
    },
    "ResultSetStatistics": {
        "TableCount": 1,
        "TablesStatistics": [
        {
          "RowCount": 1,
          "TableSize": 9
        }
      ]
    }
  }
}
```

**Свойства журнала диагностики запросов**

|Имя               |Описание
|---                |---
|RootActivityId |ИД корневого действия
|стартедон        |Время (UTC) запуска этой команды
|ластупдатедон           |Время (UTC) окончания выполнения команды
|База данных              |Имя базы данных, в которой выполнялась команда
|Состояние  |Состояние, в котором была завершена команда
|FailureReason|Причина сбоя
|тоталкпу     |Общая длительность ЦП
|ApplicationName            |Имя приложения, вызвавшего запрос
|меморипеак          |Пиковая память
|Длительность      |Длительность команды
|Пользователь|Пользователь, который вызвал запрос
|Основной        |Участник, который вызвал запрос
|сканнедекстентсстатистикс        | Содержит статистику по просканированным экстентам
|миндатасканнедтиме        |Минимальное время проверки данных
|максдатасканнедтиме        |Максимальное время проверки данных
|тоталекстентскаунт        |Общее число экстентов
|сканнедекстентскаунт        |Число проверенных экстентов
|тоталровскаунт        |Общее число строк
|сканнедровскаунт        |Число проверенных строк
|качестатистикс        |Содержит статистику кэша
|Память        |Содержит статистику памяти кэша
|Попадания        |Попаданий в кэш памяти
|"Промахи"        |Промахов в кэше памяти
|Диск        |Содержит статистику диска кэша
|Попадания        |Попаданий в кэш диска
|"Промахи"        |Промахи кэша диска
|Сегментов        |Содержит статистику кэша горячий и холодного сегментов
|Горячий        |Содержит статистику кэша активных сегментов
|хитбитес        |Попаданий в оперативный кэш сегментов
|Неодинаковые sByte        |Критические промахи в кэше сегментов
|ретриевебитес        | Байт загруженного оперативного кэша сегментов
|Холодный        |Содержит статистику кэша холодных сегментов
|хитбитес        |Число попаданий в холодный кэш для сегментов
|Неодинаковые sByte        |Промахи в холодном кэше сегментов
|ретриевебитес        |Холодный кэш, извлеченный байт сегментов
|бипассбитес        |Байты обхода кэша сегментов
|ресултсетстатистикс        |Содержит статистику результирующего набора
|TableCount        |Число таблиц результирующего набора
|таблесстатистикс        |Содержит статистику таблицы результирующего набора
|RowCount        | Число строк таблицы результирующего набора
|таблесизе        |Число строк таблицы результирующего набора

---

## <a name="next-steps"></a>Дальнейшие действия

* [Использование метрик для мониторинга работоспособности кластера](using-metrics.md)
* [Учебник. прием и запрос данных мониторинга в обозреватель данных Azure](ingest-data-no-code.md) для журналов диагностики приема
