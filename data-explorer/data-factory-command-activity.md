---
title: Используйте команды управления Azure Data Explorer на фабрике данных Azure
description: В этой теме используйте команды управления Azure Data Explorer в Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: d59e77af1cebe9edb1c026a6dc557facec958917
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497373"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Используйте действия командных данных Azure Data Factory для выполнения команд управления диспетчерами Azure Data Explorer

[Azure Data Factory](/azure/data-factory/) (ADF) — это облачный сервис интеграции данных, позволяющий выполнять комбинацию действий на данных. Используйте ADF для создания рабочих процессов на основе данных для организации и автоматизации движения данных и преобразования данных. Действия **команды Azure Data Explorer** в Azure Data Factory позволяют запускать команды управления [Azure Data Explorer](kusto/concepts/index.md#control-commands) в рабочем процессе ADF. В этой статье рассказывается о том, как создать конвейер с активностью поиска и действием ForEach, содержащим командную деятельность Azure Data Explorer.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Кластер и база данных Azure Data Explorer](create-cluster-database-portal.md)
* Источник данных.
* [Фабрика данных](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Создание конвейера

1. Выберите инструмент **карандаша автора.** 
1. Создайте новый конвейер, выбрав, **+** а затем выберите **Pipeline** из выпадения.

   ![создание нового трубопровода](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Создание действия поиска

[Активность поиска](/azure/data-factory/control-flow-lookup-activity) может получить набор данных из любых источников данных, поддерживаемых Azure Data Factory. Выход из деятельности Lookup может быть использован в ForEach или другой деятельности.

1. В панели **действий,** под **общим**, выберите **активность поиска.** Перетащите и уроните его в основной холст справа.
 
    ![выберите активность поиска](media/data-factory-command-activity/select-activity.png)

1. Теперь холст содержит созданный вид действия Lookup. Используйте вкладки под холстом, чтобы изменить все соответствующие параметры. В **общем,** переименуем действие. 

    ![отослать активность поиска](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Нажмите на пустую область холста, чтобы просмотреть свойства конвейера. Используйте **вкладку General** для переименования конвейера. Наш трубопровод называется *трубопровод-4-docs.*

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Создание набора данных Azure Data Explorer в деятельности поиска

1. В **настройках**выберите заранее созданный **набор данных**Azure Data Explorer Source или выберите **новый** набор данных.
 
    ![добавить набор данных в настройках поиска](media/data-factory-command-activity/lookup-settings.png)

1. Выберите набор данных **Azure Data Explorer (Kusto)** из **окна нового набора данных.** Выберите **Продолжить,** чтобы добавить новый набор данных.

   ![выбрать новый набор данных](media/data-factory-command-activity/select-new-dataset.png) 

1. Новые параметры набора данных Azure Data Explorer видны в **параметрах.** Для обновления параметров выберите **Edit**.

    ![Настройки поиска с набором данных Azure Data Explorer](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Новая вкладка **AzureDataExplorerTable** открывается в главном холсте. 
    * Выберите **общее** и отредагите имя набора данных. 
    * Выберите **соединение** для редектора свойств набора данных. 
    * Выберите **службу Связанных** из выпадающих или выберите **новую** для создания новой связанной службы.

    ![Отредить свойства набора данных Azure Data Explorer](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. При создании новой связанной службы открывается страница **New Linked Service (Azure Data Explorer):**

    ![ADX новый связанный сервис](media/data-factory-command-activity/adx-new-linked-service.png)

   * Выберите **службу «Имя** для исследователя данных» Azure. Добавить **Описание,** если это необходимо.
   * В **Connect через время выполнения интеграции,** изменить текущие настройки, если это необходимо. 
   * В **методе выбора учетной записи** выберите кластер с помощью одного из двух: 
        * Выберите кнопку **подписки На подписку From Azure** и выберите учетную запись **подписки Azure.** Затем выберите **кластер.** Обратите внимание, что выпадение будет только список кластеров, которые принадлежат пользователю.
        * Вместо этого выберите кнопку **«Введите вручную** радио» и введите конечную **точку** (url кластера).
    * Укажите **арендатора**.
    * Введите **идентификатор основного идентификатора служб** Основной идентификатор должен иметь соответствующие разрешения в соответствии с уровнем разрешения, требуемым используемой командой.
    * Выберите **ключевую** кнопку Службы и введите **ключевой ключ службы.**
    * Выберите **базу данных** из меню выпадения. Кроме того, выберите **флажок и** введите имя базы данных.
    * Выберите **тест-соединение** для тестирования созданного вами соединения службы. Если вы можете подключиться к настройке, появится **успешное** подключение к зеленой галечке.
    * Выберите **finish** для завершения создания связанных служб.

1. После настройки службы связи в **AzureDataExplorerTable** > **Connection**добавьте имя **таблицы.** Выберите **данные предварительного просмотра,** чтобы убедиться, что данные представлены должным образом.

   Теперь набор данных готов, и вы можете продолжить редактирование конвейера.

### <a name="add-a-query-to-your-lookup-activity"></a>Добавление запроса в действие поиска

1. В**настройках** **конвейера-4-docs** > добавляется запрос в текстовое поле **запроса,** например:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Изменение **тайм-аута запроса** или **нет усечения** и **первый ряд только** свойства, по мере необходимости. В этом потоке мы сохраняем **тайм-аут запроса** по умолчанию и отсеиваем флажки. 

    ![Окончательные настройки деятельности поиска](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Создание действия «Для каждого» 

Действие [For-Each](/azure/data-factory/control-flow-for-each-activity) используется для итерата по сбору и выполнения определенных действий в цикле. 

1. Теперь вы добавляете в конвейер действие For-Each. Это действие будет обрабатывать данные, полученные из действия Lookup. 
    * В панели **действий,** под **итерацией & Условные,** выберите **действие ForEach** и перетащите и уроните его в холст.
    * Нарисуйте линию между выходом действия Lookup и входной частью действия ForEach в холсте, чтобы соединить их.

        ![Действие ForEach](media/data-factory-command-activity/for-each-activity.png)

1.    Выберите действие ForEach в холсте. В **вкладке "Настройки"** ниже:
    * Проверьте **последовательный** флажок для последовательной обработки результатов поиска или оставьте его без внимания для создания параллельной обработки.
    * Установить **количество пакетов**.
    * В **элементах**, предоставить следующую ссылку на выходное значение: * @activity('Lookup1').output.value*

       ![Настройки действия ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Создание действия команды azure Data Explorer в рамках действия ForEach

1. Дважды щелкните действие ForEach в холсте, чтобы открыть его в новом холсте, чтобы указать действия внутри ForEach.
1. В панели **действий,** под **Azure Data Explorer,** выберите действия **команды Azure Data Explorer** Command и перетащите и перебросьте ее в холст.

    ![Командная деятельность Azure Data Explorer](media/data-factory-command-activity/adx-command-activity.png)

1.    Во вкладке **"Соединение"** выберите ту же службу Linked, созданную ранее.

    ![лазурные данные исследователь командно-успокоительных действий вкладке](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Во вкладке **"Командование"** укажите следующую команду:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Командование** поручает Azure Data Explorer экспортировать результаты данного запроса в хранилище каплей в сжатом формате. Он работает асинхронно (с помощью модификатора асинхронов).
    Запрос адреса строки базы данных каждой строки в результате действия поиска. **Тайм-аут командования** можно оставить без изменений.

    ![командная деятельность](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Командное действие имеет следующие ограничения:
    > * Предельный размер: 1 МБ отклика
    > * Ограничение по времени: 20 минут (по умолчанию), 1 час (максимум).
    > * При необходимости можно приобщить запрос к результату с помощью [AdminThen'''ru,](kusto/management/index.md#combining-queries-and-control-commands)чтобы уменьшить образовавшееся размер/время.

1.    Теперь трубопровод готов. Вы можете вернуться к главному представлению конвейера, нажав на имя конвейера.

    ![Конвейер командных данных Azure Data Explorer](media/data-factory-command-activity/adx-command-pipeline.png)

1. Выберите **Debug** перед публикацией конвейера. Прогресс конвейера можно контролировать во вкладке **«Выход».**

    ![выход командной деятельности лазурных данных](media/data-factory-command-activity/command-activity-output.png)

1. Вы можете **опубликовать все,** а затем **добавить триггер** для запуска конвейера. 

## <a name="control-command-outputs"></a>Выводы команд управления

Структура вывода командной деятельности приведена ниже. Этот вывод может быть использован следующим действием в конвейере.

### <a name="returned-value-of-a-non-async-control-command"></a>Возвратное значение команды управления, не не связанных с асинами

В команде управления не-асинами структура возвращенного значения аналогична структуре результата действия Lookup. Поле `count` указывает количество возвращенных записей. Поле фиксированного `value` массива содержит список записей. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Возвратное значение команды управления асинами

В команде управления асинами действие осваиваю таблицу операций за кулисами до завершения операции async или выбыва. Таким образом, возвращенное значение `.show operations OperationId` будет содержать результат для данного свойства **OperationId.** Проверьте значения свойств **State** и **Status,** чтобы проверить успешное завершение операции.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Следующие шаги

* Узнайте о том, как [скопировать данные в Azure Data Explorer с помощью Azure Data Factory.](data-factory-load-data.md)
* Узнайте об использовании [шаблона Azure Data Factory для массовой копии из базы данных в Azure Data Explorer.](data-factory-template.md)
