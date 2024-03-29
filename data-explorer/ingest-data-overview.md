---
title: Общие сведения о приеме данных в Azure Data Explorer
description: Сведения о различных способах приема данных в обозревателе данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.localizationpriority: high
ms.openlocfilehash: bb5e6823527a40d817eb2c9945af5bc951712363
ms.sourcegitcommit: 294130a8c44d26e62146dcc52a88e56d0f98a151
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106376374"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Общие сведения о приеме данных в Azure Data Explorer 

Прием данных — это процесс, используемый для загрузки записей данных из одного или нескольких источников для импорта данных в таблицу в Azure Data Explorer. После принятия данные становятся доступными для запроса.

На следующей схеме показан сквозной поток работы в Azure Data Explorer, в том числе разные методы приема данных.

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="Общая схема приема данных и управления ими":::

Служба управления данными в Azure Data Explorer, которая отвечает за прием данных, реализует следующий процесс:

Azure Data Explorer извлекает данные из внешнего источника и считывает запросы из очереди ожидания Azure. Данные передаются Диспетчеру данных в виде пакета или в потоковом режиме. Пакетные данные, поступающие в ту же базу данных и таблицу, оптимизируются для пропускной способности приема. Azure Data Explorer проверяет изначальные данные и при необходимости преобразует форматы данных. Далее для данных может выполняться сопоставление схемы, упорядочение, индексирование, кодирование и сжатие. Данные сохраняются в хранилище в соответствии с заданной политикой хранения. Диспетчер данных затем фиксирует прием данных в обработчике, где он становится доступен для запросов. 

## <a name="supported-data-formats-properties-and-permissions"></a>Поддерживаемые форматы данных, свойства и разрешения

* **[Поддерживаемые форматы данных.](ingestion-supported-formats.md)** 

* **[Свойства приема —](ingestion-properties.md)** свойства, влияющие на способ приема данных (например, присвоение тегов, сопоставление, время создания).

* **Разрешения**: для приема данных процессу требуются [разрешения на уровне принимающей базы данных](kusto/management/access-control/role-based-authorization.md). Для выполнения других действий, например запросов, могут потребоваться разрешения администратора базы данных, пользователя базы данных или администратора таблиц.

## <a name="batching-vs-streaming-ingestion"></a>Пакетный и потоковый приемы данных

* При пакетном приеме выполняется пакетная обработка данных и их оптимизация для высокой пропускной способности приема. Этот метод предпочтителен и является самым эффективным способом приема. Пакетная обработка данных выполняется в соответствии со свойствами приема. Небольшие пакеты данных затем объединяются и оптимизируются для быстрого получения результатов запросов. Политику [пакетного приема](kusto/management/batchingpolicy.md) можно задать для баз данных или таблиц. По умолчанию максимальное значение для пакетной обработки составляет 5 минут, 1000 элементов или общий объем в 1 ГБ.

* [Прием с потоковой передачей](ingest-data-streaming.md) — это способ с непрерывным приемом данных из источника с потоковой передачей. Потоковый прием данных позволяет уменьшить задержку до близкой к реальному времени для небольших наборов данных в каждой таблице. Данные изначально принимаются в хранилище строк, а потом перемещаются в экстенты хранилища столбцов. Потоковый прием данных можно реализовать с помощью клиентской библиотеки Azure Data Explorer или одного из поддерживаемых конвейеров данных. 

## <a name="ingestion-methods-and-tools"></a>Методы и инструменты приема

Azure Data Explorer поддерживает несколько методов приема, каждый из которых имеет собственные целевые сценарии. К таким методам относятся средства приема, соединители и подключаемые модули для различных служб, управляемые конвейеры, программный прием с помощью пакетов SDK и прямой доступ к приему.

### <a name="ingestion-using-managed-pipelines"></a>Прием с помощью управляемых конвейеров

Для организаций, которые хотят обеспечить управление (регулирование, повторные попытки, мониторы, оповещения и т. д.) с помощью внешней службы, использование соединителя будет самым оптимальным решением. Прием с постановкой в очередь подходит для больших объемов данных. Azure Data Explorer поддерживает следующие конвейеры Azure Pipelines:

* **[Сетка событий —](https://azure.microsoft.com/services/event-grid/)** конвейер, который прослушивает службу хранилища Azure и информирует Azure Data Explorer о необходимости извлечения информации при возникновении событий, на которые создана подписка. Дополнительные сведения см. в статье [Краткое руководство. Прием больших двоичных объектов Azure в Azure Data Explorer благодаря подписке на уведомления службы "Сетка событий Azure"](ingest-data-event-grid.md).

* **[Концентратор событий —](https://azure.microsoft.com/services/event-hubs/)** конвейер, передающий события из служб в Azure Data Explorer. Дополнительные сведения см. в статье [Краткое руководство. Прием данных из концентратора событий в Azure Data Explorer](ingest-data-event-hub.md).

* **[Центр Интернета вещей](https://azure.microsoft.com/services/iot-hub/)** : конвейер, используемый для передачи данных с поддерживаемых устройств Интернета вещей в Azure Data Explorer. Дополнительные сведения см. в статье [Прием из Центра Интернета вещей](ingest-data-iot-hub.md).

* **Фабрика данных Azure (ADF) —** полностью управляемая служба интеграции данных для аналитических рабочих нагрузок в Azure. Фабрика данных Azure подключается к более чем 90 поддерживаемым источникам, чтобы обеспечить эффективную и устойчивую передачу данных. ADF выполняет подготовку, преобразование и обогащение данных для предоставления аналитических сведений, которые можно отслеживать разными способами. Эту службу можно использовать как одноразовое решение, периодически или с запуском по определенным событиям. 
  * [Интеграция Azure Data Explorer с Фабрикой данных Azure.](data-factory-integration.md)
  * [Использование Фабрики данных Azure для копирования данных из поддерживаемых источников в Azure Data Explorer.](./data-factory-load-data.md)
  * [Массовое копирование из базы данных в Azure Data Explorer с помощью шаблона Фабрики данных Azure.](data-factory-template.md)
  * [Использование действий команд в Фабрике данных Azure для запуска управляющих команд Azure Data Explorer.](data-factory-command-activity.md)

### <a name="ingestion-using-connectors-and-plugins"></a>Прием с помощью соединителей и подключаемых модулей

* **Подключаемый модуль Logstash.** Дополнительные сведения см. в статье [Прием данных из Logstash в Azure Data Explorer](ingest-data-logstash.md).

* **Соединитель Kafka.** Дополнительные сведения см. в статье [Прием данных из Kafka в Azure Data Explorer](ingest-data-kafka.md).

* **[Power Automate —](https://flow.microsoft.com/)** автоматизированный конвейер рабочих процессов с передачей данных в Azure Data Explorer. Power Automate можно использовать для выполнения запроса и заданных действий, задавая результаты запросов в качестве триггеров. Подробные сведения см. в статье [Соединитель Azure Data Explorer для Power Automate (предварительная версия)](flow.md).

* **Соединитель Apache Spark —**  проект с открытым кодом, который может выполняться на любом кластере Spark. Он реализует источник и приемник данных для перемещения данных между кластерами Azure Data Explorer и Spark. Вы можете создавать быстрые и масштабируемые приложения для управляемых данными сценариев. Подробные сведения см. в статье [Соединитель Azure Data Explorer для Apache Spark](spark-connector.md).

### <a name="programmatic-ingestion-using-sdks"></a>Программный прием с помощью пакетов SDK

Обозреватель данных Azure предоставляет пакеты SDK, которые можно использовать для запросов и приема данных. Прием данных программным образом оптимизирован для снижения затрат на прием путем минимизации количества транзакций с хранилищем во время процесса приема и после него.

**Доступные пакеты SDK и проекты с открытым кодом**

* [Пакет SDK для Python](kusto/api/python/kusto-python-client-library.md)

* [Пакет SDK для .NET](kusto/api/netfx/about-the-sdk.md)

* [пакет SDK для Java](kusto/api/java/kusto-java-client-library.md)

* [Пакет SDK для Node](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [Пакет SDK для Go](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>Инструменты

* **[Прием данных одним щелчком —](ingest-data-one-click.md)** позволяет быстро принимать данные путем создания и корректировки таблиц из широкого спектра источников самых разных типов. Функция приема данных одним щелчком автоматически предлагает таблицы и структуры сопоставления на основе источника данных в Azure Data Explorer. Такой метод можно использовать для однократного приема или для определения непрерывного приема через Сетку событий в контейнере, в который передаются данные.

* **[LightIngest —](lightingest.md)** служебная программа для специализированных операций приема данных в Azure Data Explorer. Эта программа может извлекать исходные данные из локальной папки или из контейнера Хранилища BLOB-объектов Azure.

### <a name="kusto-query-language-ingest-control-commands"></a>Управляющие команды приема в языке запросов Kusto

Существует ряд методов, с помощью которых данные могут быть приняты непосредственно обработчиком с использованием команд языка запросов Kusto (KQL). Так как эти методы обходят службы управления данными, они подходят только для экспериментирования и прототипирования. Не используйте этот метод в производственных сценариях или сценариях с обработкой крупных объемов данных.

  * **Встроенный прием —**  в обработчик передается управляющая команда ([.ingest inline](kusto/management/data-ingestion/ingest-inline.md)), в текст которой сразу включаются данные для приема. Этот метод предназначен для импровизированного тестирования.

  * **Прием из запроса**. В обработчик отправляется управляющая команда ([.set, .append, .set-or-append или .set-or-replace](kusto/management/data-ingestion/ingest-from-query.md)), которая опосредованно определяет данные по результатам запроса или команды.

  * **Прием из хранилища (вытягивание)** . В обработчик отправляется управляющая команда ([.ingest into](kusto/management/data-ingestion/ingest-from-storage.md)), а данные хранятся во внешнем хранилище (например, Хранилище BLOB-объектов Azure), которое доступно для этого обработчика и на которое указывает эта команда.

## <a name="comparing-ingestion-methods-and-tools"></a>Сравнение методов и инструментов приема

| Название метода приема | Тип данных | Максимальный размер файла | Потоковая передача, пакетная обработка, непосредственный прием | Самые распространенные сценарии | Рекомендации |
| --- | --- | --- | --- | --- | --- |
| [**Прием данных одним щелчком**](ingest-data-one-click.md) | *sv, JSON | 1 ГБ без сжатия (см. примечание)| Пакетная обработка с передачей в контейнер, локальный файл и BLOB-объект при прямом приеме | Однократный прием, создание схемы таблицы, определение непрерывного приема с помощью Сетки событий, массовый прием с помощью контейнера (до 10 000 BLOB-объектов) | 10 000 BLOB-объектов выбираются из контейнера случайным образом|
| [**LightIngest**](lightingest.md) | Поддерживаются все форматы | 1 ГБ без сжатия (см. примечание) | Пакетная обработка через диспетчер данных или прямой прием в обработчик |  Перенос данных, исторические данные со скорректированными отметками времени приема, массовый прием (без ограничений на размер)| С учетом регистра, с учетом пробела |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX в Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Фабрика данных Azure (ADF)**](./data-factory-integration.md) | [Поддерживаемые форматы данных](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | не ограничен *(в соответствии с ограничениями ADF) | Пакетная обработка или на каждый триггер ADF | Поддерживает форматы, которые обычно не поддерживаются, крупные файлы, может выполнять копирование из более 90 источников, из локальной среды в облако | Для приема данных с помощью этого метода требуется относительно больше времени. ADF передает все данные в память, а затем начинает прием. |
|[ **Поток данных Azure**](./flow.md) | | | | Команды приема как часть потока| Время ответа должно быть небольшим |
| [**Центр Интернета вещей**](ingest-data-iot-hub-overview.md) | [Поддерживаемые форматы данных](ingest-data-iot-hub-overview.md#data-format)  | Н/Д | Пакетная обработка, потоковая передача | Сообщения, события, свойства Интернета вещей | |
| [**Концентратор событий**](ingest-data-event-hub-overview.md) | [Поддерживаемые форматы данных](ingest-data-event-hub-overview.md#data-format) | Н/Д | Пакетная обработка, потоковая передача | Сообщения, события | |
| [**Сетка событий**](ingest-data-event-grid-overview.md) | [Поддерживаемые форматы данных](ingest-data-event-grid-overview.md#data-format) | 1 ГБ без сжатия | Пакетная обработка | Непрерывный прием из службы хранилища Azure, внешние данные в службе хранилища Azure | 100 КБ — это оптимальный размер файла, который используется для переименования и создания BLOB-объектов |
| [**Пакет SDK для .NET**](./net-sdk-ingest-data.md) | Поддерживаются все форматы | 1 ГБ без сжатия (см. примечание) | Пакетная обработка, потоковая передача, прямой прием | Написание собственного кода в соответствии с потребностями организации |
| [**Python**](python-ingest-data.md) | Поддерживаются все форматы | 1 ГБ без сжатия (см. примечание) | Пакетная обработка, потоковая передача, прямой прием | Написание собственного кода в соответствии с потребностями организации |
| [**Node.js**](node-ingest-data.md) | Поддерживаются все форматы | 1 ГБ без сжатия (см. примечание) | Пакетная обработка, потоковая передача, прямой прием | Написание собственного кода в соответствии с потребностями организации |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | Поддерживаются все форматы | 1 ГБ без сжатия (см. примечание) | Пакетная обработка, потоковая передача, прямой прием | Написание собственного кода в соответствии с потребностями организации |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | Поддерживаются все форматы | 1 ГБ без сжатия (см. примечание) | Пакетная обработка, потоковая передача, прямой прием| Написание собственного кода в соответствии с потребностями организации |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | Поддерживаются все форматы | 1 ГБ без сжатия (см. примечание) | Пакетная обработка, потоковая передача, прямой прием | Написание собственного кода в соответствии с потребностями организации |

> [!Note] 
> Если это примечание упоминается в таблице выше, для приема поддерживается файл размером не более 4 ГБ. Для приема мы советуем файлы размером от 100 МБ до 1 ГБ.

## <a name="ingestion-process"></a>Процесс приема

После выбора подходящего метода приема сделайте следующее:

1. **Задайте политику хранения.**

    К данным, принятым в таблицу Azure Data Explorer, применяется действующая для таблицы политика хранения. Если политика хранения не задана для таблицы явным образом, она создается на основе политики хранения базы данных. Хранение на горячем уровне зависит от размера кластера и вашей политики хранения. При приеме данных, объем которых превышает доступное пространство, первые полученные данные будут перенесены на холодный уровень.
    
    Убедитесь, что политика хранения базы данных соответствует вашим потребностям. В противном случае явно переопределите ее на уровне таблицы. Дополнительные сведения см. в статье [Политика хранения](kusto/management/retentionpolicy.md). 
    
1. **Создание таблицы**

    Для приема данных необходимо заранее создать таблицу. Используйте один из следующих методов.
   * Создайте таблицу с помощью [команды](kusto/management/create-table-command.md). 
   * Создайте таблицу с использованием [приема одним щелчком](one-click-ingestion-new-table.md).

    > [!Note]
    > Если запись является неполной или поле не может быть проанализировано в необходимом типе данных, соответствующие столбцы таблицы заполняются значениями NULL.

1. **Создайте сопоставление схем.**

    [Сопоставление схем](kusto/management/mappings.md) помогает привязать поля исходных данных к столбцам таблицы назначения. Сопоставление позволяет принимать данные из разных источников в одну таблицу на основе определенных атрибутов. Поддерживаются разные типы сопоставлений, как для строк (CSV, JSON и AVRO), так и для столбцов (Parquet). Большинство методов также позволяют [предварительно создать сопоставления в таблице](kusto/management/create-ingestion-mapping-command.md) и ссылаться на них с помощью параметра команды приема.

1. **Задайте политику обновления** (необязательно).

   Некоторые сопоставления форматов данных (Parquet, JSON и Avro) поддерживают простые и полезные преобразования во время приема. Если сценарий требует более сложной обработки во время приема, используйте политику обновления, которая позволяет обеспечить простую обработку с помощью команд языка запросов Kusto. Политика обновления автоматически выполняет извлечение и преобразование принятых данных в исходной таблице и принимает результирующие данные в одну или несколько таблиц назначения. Задайте свою [политику обновления](kusto/management/update-policy.md).



## <a name="next-steps"></a>Дальнейшие действия

* [Поддерживаемые форматы данных](ingestion-supported-formats.md)
* [Поддерживаемые свойства приема](ingestion-properties.md)
