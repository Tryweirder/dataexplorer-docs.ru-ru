---
title: Копирование из базы данных в обозреватель данных Azure с помощью шаблона фабрики данных Azure
description: Из этой статьи вы узнаете, как использовать шаблон фабрики данных Azure для копирования из базы данных в Azure обозреватель данных
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/08/2019
ms.openlocfilehash: d0e7cb8badcd5ae3ad3939728f35de435b848735
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873276"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Копирование из базы данных в обозреватель данных Azure с помощью шаблона фабрики данных Azure 

Azure обозреватель данных — это быстрая, полностью управляемая служба аналитики данных. Он обеспечивает анализ больших объемов данных в реальном времени, таких как приложения, веб-сайты и устройства Интернета вещей, из различных источников. 

Чтобы скопировать данные из базы данных на сервере Oracle, Netezza, Teradata или SQL Server в Azure обозреватель данных, необходимо загрузить огромные объемы данных из нескольких таблиц. Обычно данные должны быть разделены в каждой таблице, чтобы можно было загружать строки из одной таблицы с использованием нескольких параллельных потоков. В этой статье описывается шаблон для этих сценариев.

[Шаблоны фабрики данных Azure](/azure/data-factory/solution-templates-introduction) — это предопределенные конвейеры фабрики данных. Эти шаблоны позволяют быстро приступить к работе с фабрикой данных и сократить время разработки проектов интеграции данных. 

С помощью операций *поиска* и *foreach* вы создадите шаблон " *групповое копирование из базы данных в Azure обозреватель данных* ". Для ускорения копирования данных можно использовать шаблон, чтобы создать множество конвейеров для каждой базы данных или для каждой таблицы. 

> [!IMPORTANT]
> Обязательно используйте средство, которое подходит для количества данных, которое необходимо скопировать.
> * Используйте шаблон " *групповое копирование из базы данных в Azure обозреватель данных* " для копирования больших объемов данных из баз данных, таких как SQL Server и Google BigQuery, в Azure обозреватель данных. 
> * Используйте [*средство копирование данных фабрики данных*](data-factory-load-data.md) , чтобы скопировать несколько таблиц с небольшими или средними объемами данных в обозреватель данных Azure. 

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Кластер и база данных Azure Data Explorer](create-cluster-database-portal.md).
* [Создайте фабрику данных](data-factory-load-data.md#create-a-data-factory).
* Источник данных в базе данных.

## <a name="create-controltabledataset"></a>Создание Контролтабледатасет

*Контролтабледатасет* указывает, какие данные будут скопированы из источника в место назначения в конвейере. Число строк указывает общее число конвейеров, необходимых для копирования данных. Необходимо определить Контролтабледатасет как часть базы данных источника.

Пример формата исходной таблицы SQL Server показан в следующем коде:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Элементы кода описаны в следующей таблице.

|Свойство  |Описание  | Пример
|---------|---------| ---------|
|PartitionId   |  Порядок копирования | 1  |  
|саурцекуери   |  Запрос, указывающий, какие данные будут скопированы во время выполнения конвейера | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|адкстабленаме  |  Имя целевой таблицы | мядкстабле       |  

Если Контролтабледатасет имеет другой формат, создайте сравнимый Контролтабледатасет для вашего формата.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Использование шаблона "групповое копирование из базы данных в Azure" обозреватель данных

1. На панели **Приступая к работе** выберите **создать конвейер из шаблона** , чтобы открыть панель **Коллекция шаблонов** .

    ![Панель "Приступая к работе" фабрики данных Azure](media/data-factory-template/adf-get-started.png)

1. Выберите шаблон " **групповое копирование из базы данных в Azure обозреватель данных** ".
 
    ![Шаблон "полное копирование из базы данных в Azure обозреватель данных"](media/data-factory-template/pipeline-from-template.png)

1.  В области **групповое копирование из базы данных в Azure обозреватель данных** в разделе **входные данные пользователя**укажите наборы данных, выполнив следующие действия. 

    а. В раскрывающемся списке **контролтабледатасет** выберите связанную службу для таблицы управления, которая указывает, какие данные копируются из источника в место назначения и куда они будут помещены в место назначения. 

    b. В раскрывающемся списке **SourceDataset** выберите связанную службу для базы данных-источника. 

    c. В раскрывающемся списке **азуредатаексплорертабле** выберите таблицу Azure обозреватель данных. Если набор данных не существует, [Создайте связанную службу Azure обозреватель данных](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) , чтобы добавить набор данных.

    d. Щелкните **Использовать этот шаблон**.

    ![Область "полное копирование из базы данных в Azure обозреватель данных"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Выберите область на холсте вне действий, чтобы получить доступ к конвейеру шаблона. Перейдите на вкладку **Параметры** , чтобы ввести параметры для таблицы, включая **имя** (имя таблицы управления) и **значение по умолчанию** (имена столбцов).

    ![Параметры конвейера](media/data-factory-template/pipeline-parameters.png)

1.  В разделе **Поиск**выберите **жетпартитионлист** , чтобы просмотреть параметры по умолчанию. Запрос создается автоматически.
1.  Выберите действие команды, **фореачпартитион**, перейдите на вкладку **Параметры** и выполните следующие действия.

    а. В поле **число пакетов** введите число от 1 до 50. Этот выбор определяет количество параллельно выполняемых конвейеров до тех пор, пока не будет достигнуто число *контролтабледатасет* строк. 

    b. Чтобы обеспечить параллельное выполнение пакетов конвейера, *не* выбирайте **последовательный** флажок.

    ![Параметры Фореачпартитион](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Рекомендуется выполнять несколько конвейеров параллельно, чтобы можно было быстрее копировать данные. Чтобы повысить эффективность, разбейте данные в исходной таблице и распределите по одному разделу на конвейер в соответствии с датой и таблицей.

1. Выберите **проверить все** , чтобы проверить конвейер фабрики данных Azure, а затем просмотрите результат в области **выходные данные проверки конвейера** .

    ![Проверка конвейеров шаблонов](media/data-factory-template/validate-template-pipelines.png)

1. При необходимости выберите **Отладка**, а затем щелкните **добавить триггер** для запуска конвейера.

    ![Кнопки "Отладка" и "Запуск конвейера"](media/data-factory-template/trigger-run-of-pipeline.png)    

Теперь можно использовать шаблон для эффективного копирования больших объемов данных из баз данных и таблиц.

## <a name="next-steps"></a>Дальнейшие шаги

* Узнайте, как [Копировать данные в azure обозреватель данных с помощью фабрики данных Azure](data-factory-load-data.md).
* Сведения о [соединителе azure обозреватель данных](/azure/data-factory/connector-azure-data-explorer) в фабрике данных Azure.
* Дополнительные сведения о запросах [Обозреватель данных Azure](web-query-data.md) для запросов к данным.






