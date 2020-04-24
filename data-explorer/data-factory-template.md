---
title: Копирование оптом из базы данных в Azure Data Explorer с помощью шаблона Azure Data Factory
description: В этой статье вы научитесь использовать шаблон Azure Data Factory для копирования оптом из базы данных в Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498244"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Копирование оптом из базы данных в Azure Data Explorer с помощью шаблона Azure Data Factory 

Azure Data Explorer — это быстрое, полностью управляемое сервис анализа данных. Он предлагает анализ больших объемов данных, которые передаются из многих источников, таких как приложения, веб-сайты и устройства IoT. 

Чтобы скопировать данные из базы данных в Oracle Server, Netezza, Teradata или S'L Server в Azure Data Explorer, необходимо загрузить огромные объемы данных из нескольких таблиц. Обычно данные должны быть разделены в каждой таблице, так что вы можете загружать строки с несколькими потоками параллельно из одной таблицы. В этой статье описывается шаблон для использования в этих сценариях.

[Шаблоны Azure Data Factory](/azure/data-factory/solution-templates-introduction) являются предопределенными конвейерами Data Factory. Эти шаблоны могут помочь вам быстро начать работу с Data Factory и сократить время разработки проектов интеграции данных. 

Вы создаете *массовую копию из базы данных в* шаблон Azure Data Explorer с помощью действий *Lookup* и *ForEach.* Для более быстрого копирования данных можно использовать шаблон для создания большого количества конвейеров для базы данных или таблицы. 

> [!IMPORTANT]
> Обязательно используйте инструмент, подходящий для количества данных, которые вы хотите скопировать.
> * Используйте *шаблон Bulk Copy от базы данных до* шаблона Azure Data Explorer для копирования больших объемов данных из баз данных, таких как сервер S'L и Google Big's'ry, для исследователя данных Azure. 
> * Используйте [*инструмент копирования данных Data Factory*](data-factory-load-data.md) для копирования нескольких таблиц с небольшими или умеренными объемами данных в Azure Data Explorer. 

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Кластер и база данных Azure Data Explorer](create-cluster-database-portal.md).
* [Создание фабрики данных.](data-factory-load-data.md#create-a-data-factory)
* Источник данных в базе данных.

## <a name="create-controltabledataset"></a>Создание набора данных о контроле

*ControlTableDataset* показывает, какие данные будут скопированы из источника в пункт назначения в конвейере. Количество строк указывает общее количество конвейеров, необходимых для копирования данных. Необходимо определить ControlTableDataset как часть исходной базы данных.

Пример формата исходной таблицы сервера S'L Server отображается в следующем коде:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Элементы кода описаны в следующей таблице:

|Свойство  |Описание  | Пример
|---------|---------| ---------|
|PartitionId   |  Заказ на копирование | 1  |  
|Источникивай   |  Запрос, указывающий, какие данные будут скопированы во время выполнения конвейера | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Имя таблицы назначения | MyAdxTable       |  

Если ваш набор ControlTableDataset находится в другом формате, создайте сопоставимый набор ControlTableDataset для вашего формата.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Используйте массовую копию из базы данных в шаблон Azure Data Explorer

1. В панели **Let's Get started,** выберите **Создать конвейер из шаблона,** чтобы открыть панель **галереи шаблонов.**

    ![Панель данных Azure "Давайте начнем"](media/data-factory-template/adf-get-started.png)

1. Выберите **массовую копию из базы данных в шаблон Azure Data Explorer.**
 
    ![Шаблон "Массовая копия от базы данных к исследователю данных Azure"](media/data-factory-template/pipeline-from-template.png)

1.  В **массовой копии от базы данных до** панели Azure Data Explorer под **входными данными пользователя**укажите наборы данных, сделав следующее: 

    а. В списке отсева **ControlTableDataset** выберите связанную службу с таблицей управления, которая показывает, какие данные копируется из источника в пункт назначения и где они будут размещены в пункте назначения. 

    b. В списке выпадающих **данных SourceDataset** выберите связанную службу с исходной базой данных. 

    c. В списке выпадающих данных **AzureDataExplorerTable** выберите таблицу Azure Data Explorer. Если набор данных не существует, [создайте службу, связанную с данными данных Azure Data Explorer,](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) для добавления набора данных.

    d. Выберите **Использовать этот шаблон**.

    ![Панель "Массовая копия от базы данных к исследователю данных Azure"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Выберите область в холсте, вне действия, для доступа к шаблону конвейера. Выберите вкладку **Параметры** для ввода параметров для таблицы, включая **имя** (имя таблицы управления) и **значение по умолчанию** (имена столбцов).

    ![Параметры конвейера](media/data-factory-template/pipeline-parameters.png)

1.  В рамках **поиска**выберите **GetPartitionList** для просмотра настроек по умолчанию. Запрос создается автоматически.
1.  Выберите действие Команды, **ForEachPartition**, выберите вкладку **«Настройки»,** а затем сделайте следующее:

    а. В поле **отсчета пакета** введите число от 1 до 50. Этот выбор определяет количество конвейеров, которые работают параллельно, пока не будет достигнуто количество строк *ControlTableDataset.* 

    b. Чтобы пакеты конвейера были выполняться параллельно, *не* **выбирайте** последовательный флажок.

    ![Настройки для каждого места](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Наилучшей практикой является параллельное запуск многих конвейеров, чтобы можно было быстрее копировать данные. Для повышения эффективности разделите данные в исходной таблице и распределите одну раздельность на конвейер в соответствии с датой и таблицей.

1. Выберите **Validate All** для проверки конвейера Azure Data Factory, а затем просмотрите результат в панели **вывода проверки конвейера.**

    ![Проверка шаблонов конвейеров](media/data-factory-template/validate-template-pipelines.png)

1. При необходимости выберите **Debug,** а затем выберите **триггер Добавить** для запуска конвейера.

    ![Кнопки "Отек" и "Запуск конвейера"](media/data-factory-template/trigger-run-of-pipeline.png)    

Теперь шаблон можно использовать для эффективной копирования больших объемов данных из баз данных и таблиц.

## <a name="next-steps"></a>Следующие шаги

* Узнайте, как [скопировать данные в Azure Data Explorer с помощью Azure Data Factory.](data-factory-load-data.md)
* Узнайте о [разъеме Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) на фабрике данных Azure.
* Узнайте о [запросах Azure Data Explorer](/azure/data-explorer/web-query-data) для запроса данных.






