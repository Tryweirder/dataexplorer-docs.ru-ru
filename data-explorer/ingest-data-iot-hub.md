---
title: Внушая данные из Концентратора IoT в Azure Data Explorer
description: В этой статье вы узнаете, как глотать (загружать) данные в Azure Data Explorer из Концентратора IoT.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1e04b3c2796f8d8814e21763d9a62189b4dc06cb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500389"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Внушая данные из Концентратора IoT в Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Портал](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Шаблон Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure Data Explorer предлагает проглатку (загрузку данных) от IoT Hub, платформы потоковой передачи больших данных и службы приема IoT.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создайте [кластер тестирования и базу данных](create-cluster-database-portal.md) с *тестом*имени базы данных.
* [Пример приложения](https://github.com/Azure-Samples/azure-iot-samples-csharp) и документации для имитации устройства.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) для запуска примера приложения.

## <a name="create-an-iot-hub"></a>Создание концентратора Iot

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Регистрация устройства в концентраторе IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Создание целевой таблицы в обозревателе данных Azure

Теперь вы создаете таблицу в Azure Data Explorer, в которую концентраторы IoT будут отправлять данные. Вы создаете таблицу в кластере и базу данных, подготовленную в [**Prerequisites**](#prerequisites).

1. На портале Azure перейдите в свой кластер и выберите **Запрос**.

    ![Запрос ADX на портале](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Скопируйте следующую команду в окно и выберите **Выполнить**, чтобы создать таблицу (TestTable), которая будет принимать входящие данные.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Выполнение создания запроса](media/ingest-data-iot-hub/run-create-query.png)

1. Скопируйте следующую команду в окно и выберите **Выполнить** для сопоставления входящих данных JSON с именами столбцов и типами данных таблицы (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Подключение таблицы исследователя данных Azure к концентратору IoT

Теперь вы подключитесь к концентратору IoT от Azure Data Explorer. Когда это соединение завершено, данные, которые впадают в потоки концентратора iot к [целевой таблице, которую вы создали,](#create-a-target-table-in-azure-data-explorer)—

1. Выберите **Уведомления** на панели инструментов, чтобы убедиться в успешном развертывании Концентратора IoT.

1. В созданном кластере вывыберите **базы данных,** а затем выберите базу данных, созданную **тестбом.**
    
    ![Выбор тестовой базы данных](media/ingest-data-iot-hub/select-database.png)

1. Выберите **Прием данных** и **Добавить подключение к данным**. Заполните форму, указав следующую информацию. Выберите **Создать,** когда вы закончите.

    ![Соединение концентратора IoT](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Источник данных**:

    **Параметр** | **Описание поля**
    |---|---|
    | Имя подключения к данным | Имя соединения, которое необходимо создать в Azure Data Explorer
    | Центр Интернета вещей | Имя Центра Интернета вещей |
    | Политика общего доступа | Имя политики общего доступа. Должны иметь разрешение на чтение |
    | Группа потребителей |  Группа потребителей, определенная в встроенной точке IoT |
    | Свойства системы событий | [Свойства системы событий IoT Hub.](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages) При добавлении свойств системы [создавайте](kusto/management/create-table-command.md) или [обновляйте](kusto/management/alter-table-command.md) схему таблицы и [отображение,](kusto/management/mappings.md) чтобы включить выбранные свойства. | | | 

    > [!NOTE]
    > В случае [сбоя в руководстве](/azure/iot-hub/iot-hub-ha-dr#manual-failover)необходимо воссоздать соединение данных.

    **Целевая таблица**:

    Существуют два варианта маршрутизации принятых данных: *статическая* и *динамическая*. 
    В этой статье используется статическая маршрутизация, для которой нужно указать имя таблицы, формат данных и сопоставление. Поэтому не устанавливайте флажок **Мои данные содержат сведения о маршрутизации**.

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Таблица | *TestTable* | Таблица, созданная в **testdb.** |
    | Формат данных | *JSON* | Поддерживаемые форматы: Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE и TXT. |
    | Сопоставление столбцов | *TestMapping* | [Картирование,](kusto/management/mappings.md) созданное в **testdb,** которое отображает поступающие данные JSON к именам столбцов и типам данных **testdb.** Требуется для JSON, MULTILINE JSON и AVRO, а также для других форматов.|
    | | |

    > [!NOTE]
    > * Выберите **My data includes routing info** (Мои данные содержат сведения о маршрутизации) для использования динамической маршрутизации, при которой данные содержат необходимые сведения о маршрутизации, как показано в комментариях [примера приложения](https://github.com/Azure-Samples/event-hubs-dotnet-ingest). Если заданы свойства статической и динамической маршрутизации, то свойство динамической маршрутизации переопределяет свойство статической. 
    > * Попамейтеся только события, оканивающиеся после создания соединения данных.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Создание выборочных данных для тестирования

Приложение имитированного устройства подключается к конечной точке конкретного устройства Центра Интернета вещей и отправляет имитированную телеметрию температуры и влажности.

1. Скачайте пример проекта C# по ссылке https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip и извлеките ZIP-архив.

1. В окне терминала на локальном компьютере перейдите в корневую папку примера проекта C#. Затем перейдите в папку **iot-hub\Quickstarts\simulated-device**.

1. Откройте файл **SimulatedDevice.cs** в любом текстовом редакторе.

    Замените значение `s_connectionString` переменной строкой соединения устройства от [регистрации устройства на концентратор IoT.](#register-a-device-to-the-iot-hub) Сохраните изменения в файле **SimulatedDevice.cs**.

1. Установите необходимые пакеты приложения имитированного устройства, выполнив в окне терминала на локальном компьютере следующие команды:

    ```cmd/sh
    dotnet restore
    ```

1. Создайте и запустите приложение имитированного устройства, выполнив в окне терминала на локальном компьютере следующие команды:

    ```cmd/sh
    dotnet run
    ```

    На следующем снимке экрана показан пример выходных данных, когда приложение имитированного устройства отправляет данные телеметрии в Центр Интернета вещей:

    ![Запуск виртуального устройства](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Просмотр потока данных

С помощью данных приложения теперь можно увидеть поток данных от концентратора IoT к таблице в кластере.

1. На портале Azure под концентратором IoT можно увидеть всплеск активности во время работы приложения.

    ![Метрики Центра Интернета вещей](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Чтобы проверить, сколько сообщений поступило в базу данных к этому моменту, выполните следующий запрос в тестовой базе данных.

    ```Kusto
    TestTable
    | count
    ```

1. Чтобы увидеть содержимое сообщений, выполните следующий запрос:

    ```Kusto
    TestTable
    ```

    Набор результатов:
    
    ![Отображать результаты, полученные в шове данных](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * В Azure Data Explorer настроена политика агрегирования (пакетной обработки) для приема данных, предназначенных для оптимизации процесса. Политика настроена на 5 минут или 500 МБ данных, по умолчанию, так что вы можете столкнуться с задержкой. Просмотрите [политику пакетной обработки](kusto/management/batchingpolicy.md) для параметров статической обработки. 
    > * Назначьте таблицу для поддержки потоковой передачи и удаления задержки во времени отклика. Посмотреть [политику потоковой передачи.](kusto/management/streamingingestionpolicy.md) 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать ваш Концентратор IoT снова, очистите **тест-хаб-rg,** чтобы избежать расходов.

1. На портале Azure выберите **Группа ресурсов** слева, а затем выберите созданную группу ресурсов.  

    Если левое меню свернуто, нажмите ![кнопку "Развернуть",](media/ingest-data-event-hub/expand.png) чтобы развернуть его.

   ![Выбор удаляемой группы ресурсов](media/ingest-data-event-hub/delete-resources-select.png)

1. В разделе **test-resource-group** выберите **Удалить группу ресурсов**.

1. В новом окне введите имя удаляемой группы ресурсов (*test-hub-rg*) и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Следующие шаги

* [Данные запросов в Azure Data Explorer](web-query-data.md)
