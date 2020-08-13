---
title: Обзор средств и интеграции Azure обозреватель данных — Azure обозреватель данных
description: В этой статье описываются средства и интеграции в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 9522ee72e639c967f7b735c9c52d67c236498522
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201703"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Обзор средств и интеграции Azure обозреватель данных

Azure обозреватель данных — это быстрый, полностью управляемый сервис аналитики данных для анализа больших объемов потоковой передачи данных из приложений, веб-сайтов, устройств IoT и т. д. Azure обозреватель данных собирает, хранит и анализирует различные данные для улучшения продуктов, улучшения взаимодействия с клиентами, мониторинга устройств и повышения эффективности операций. 

Azure обозреватель данных предлагает различные средства и интеграции для приема данных, запросов, визуализации, оркестрации и многого другого. В дополнение к собственным службам Azure обозреватель данных позволяет пользователям легко интегрироваться с различными продуктами и платформами, включать различные варианты использования клиентов, оптимизировать бизнес-процесс путем оптимизации рабочих процессов и снижения затрат. 

В этой статье вы поставляете список средств обозреватель данных Azure, соединителей и интеграции со ссылками на соответствующие документы для получения дополнительных сведений.

## <a name="ingest-data"></a>Прием данных 

Прием данных — это процесс, который используется для загрузки записей данных из одного или нескольких источников в обозреватель данных Azure. После принятия данные становятся доступными для запроса. Azure обозреватель данных предоставляет несколько средств и соединителей для приема данных. 

### <a name="azure-data-explorer-ingestion-tools"></a>Средства приема обозреватель данных Azure

* [LightIngest](lightingest.md) — служебная программа для специализированных операций приема данных в Azure Data Explorer.
* Прием одним щелчком
    * [Общие сведения о приеме одним щелчком](ingest-data-one-click.md) 
    * [Прием данных из контейнера в новую таблицу](one-click-ingestion-new-table.md)
    * [Прием данных из локального файла в существующую таблицу](one-click-ingestion-existing-table.md)

### <a name="ingestion-integrations"></a>Интеграция приема

* Концентратор событий
    * [Прием данных из концентратора событий](ingest-data-event-hub-overview.md)
    * Прием из концентратора событий с помощью шаблона [портал Azure](ingest-data-event-hub.md), [C#](data-connection-event-hub-csharp.md), [Python](data-connection-event-hub-python.md) или [Azure Resource Manager](data-connection-event-hub-resource-manager.md)
* Сетка событий
    * [Прием из сетки событий](ingest-data-event-grid-overview.md)
    * Прием из сетки событий с помощью шаблона [портал Azure](ingest-data-event-grid.md), [C#](data-connection-event-grid-csharp.md), [Python](data-connection-event-grid-python.md) или [Azure Resource Manager](data-connection-event-grid-resource-manager.md)
* Центр Интернета вещей
    * [Прием данных из Центра Интернета вещей](ingest-data-iot-hub-overview.md)
    * Прием из центра Интернета вещей с помощью шаблона [портал Azure](ingest-data-iot-hub.md), [C#](data-connection-iot-hub-csharp.md), [Python](data-connection-iot-hub-python.md) или [Azure Resource Manager](data-connection-iot-hub-resource-manager.md)
* [Logstash](ingest-data-logstash.md).
* Фабрика данных Azure
    * [Интеграция с фабрикой данных Azure](data-factory-integration.md)
    * [Копирование данных](data-factory-load-data.md)
    * [Копирование из базы данных в оптовой среде с помощью шаблона фабрики данных Azure](data-factory-template.md)
    * [Использование действия команды фабрики данных Azure для запуска команд управления обозреватель данных Azure](data-factory-command-activity.md)
* Apache 
    * [Spark](spark-connector.md)
    * [Kafka](ingest-data-kafka.md)
* [База данных Cosmos](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>Данные запросов

### <a name="azure-data-explorer-query-tools"></a>Средства запросов обозреватель данных Azure

Существует несколько средств, доступных для выполнения запросов в обозреватель данных Azure.

* Kusto.Explorer
    * [Установка и пользовательский интерфейс](kusto/tools/kusto-explorer.md)
    * [Использование Kusto.Explorer](kusto/tools/kusto-explorer-using.md)
    * [options](kusto/tools/kusto-explorer-options.md)
    * Дополнительные разделы: [Устранение неполадок](kusto/tools/kusto-explorer-troubleshooting.md), [сочетания клавиш](kusto/tools/kusto-explorer-shortcuts.md), [рефакторинг кода](kusto/tools/kusto-explorer-refactor.md), [Навигация по коду](kusto/tools/kusto-explorer-codenav.md)и [анализ кода](kusto/tools/kusto-explorer-code-analyzer.md)
* [Веб-интерфейс](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-integrations"></a>Интеграция запросов

* [Azure Monitor](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Apache Spark](spark-connector.md)
* Microsoft Power Apps
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="visualizations-dashboards-and-reporting"></a>Средства визуализации, мониторинга и отчетности

В [обзоре визуализации](viz-overview.md) представлены подробные сведения о визуализации данных, панелях мониторинга и параметрах создания отчетов. 

## <a name="notebook-connectivity"></a>Подключение к записной книжке

* [Записные книжки Azure](azure-notebooks.md)
* [Jupyter Notebook](kqlmagic.md)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="orchestration"></a>Оркестрация

* Power Automate
    * [Соединитель автоматизации потока Power автоматизиру](flow.md)
    * [Примеры использования соединителя Power Automate](flow-usage.md)
* [Приложение логики Microsoft](kusto/tools/logicapps.md) 
* [Фабрика данных Azure](data-factory-integration.md).

## <a name="share-data"></a>Совместное использование данных

* [Azure Data Share](data-share.md)

## <a name="source-control-integration"></a>Интеграция системы управления версиями

* [Azure Pipelines](devops.md) 
* [Синхронизация Kusto](kusto/tools/synckusto.md) 

<!--Open Source Tools-->
