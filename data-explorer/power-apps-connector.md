---
title: Создание приложения Power Apps для запроса данных в Azure обозреватель данных
description: Узнайте, как создать приложение в Power Apps на основе данных в Azure обозреватель данных
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 4b74b3aa7a765f6d54454e84dfeaeac2f6bfedd6
ms.sourcegitcommit: 88291fd9cebc26e5210463cb95be5540bf84eef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437681"
---
# <a name="create-power-apps-application-to-query-data-in-azure-data-explorer-preview"></a>Создание приложения Power Apps для запроса данных в обозреватель данных Azure (Предварительная версия)

Azure обозреватель данных — это быстрый, полностью управляемый сервис аналитики данных для анализа больших объемов потоковой передачи данных из приложений, веб-сайтов, устройств IoT и т. д.

Power Apps — это набор приложений, служб, соединителей и платформ данных, обеспечивающий быструю среду разработки приложений для создания пользовательских приложений, подключающихся к бизнес-данным. Соединитель Power Apps особенно полезен при наличии большого и растущего количества потоковых данных в Azure обозреватель данных и требуется создать небольшой код, строго функциональное приложение для использования этих данных. В этой статье мы создадим приложение Power Apps для запроса данных Azure обозреватель данных. В ходе этого процесса вы увидите шаги параметризации, извлечения и представления данных.

## <a name="prerequisites"></a>Предварительные требования

* Лицензия платформа Power. Начните с [https://powerapps.microsoft.com](https://powerapps.microsoft.com) .
* Знакомство с [набором Power Apps](https://docs.microsoft.com/powerapps/powerapps-overview).

## <a name="connect-to-azure-data-explorer-connector"></a>Подключение к соединителю Azure обозреватель данных

1. Перейдите к [https://make.preview.powerapps.com/](https://make.preview.powerapps.com/) и войдите в систему.

1. В меню слева выберите **подключения** .
1. Выберите **+ создать подключение**.

    :::image type="content" source="media/power-apps-connector/new-connection.png" alt-text="Создание нового подключения в Power Apps":::

1. Выполните поиск **Обозреватель данных Azure** на панели поиска. Выберите **Azure обозреватель данных** на основе результирующих параметров.

    :::image type="content" source="media/power-apps-connector/search-adx.png" alt-text="Создание нового подключения в Power Apps":::

1. Выберите **создать** в всплывающем окне **Обозреватель данных Azure** . При необходимости укажите учетные данные.

    :::image type="content" source="media/power-apps-connector/create-connector.png" alt-text="Создание нового подключения в Power Apps":::

## <a name="create-app"></a>Создание приложения

1. Перейдите к Power Apps и выберите **приложения** в меню слева.
1. Выберите **+ новое приложение** в строке меню.
1. Выберите **Canvas** из полученного раскрывающегося списка.

    :::image type="content" source="media/power-apps-connector/create-new-app.png" alt-text="Создание нового подключения в Power Apps":::

1. В разделе **пустое приложение** выберите **Макет планшета**.

    :::image type="content" source="media/power-apps-connector/blank-canvas.png" alt-text="Создание нового подключения в Power Apps":::

### <a name="add-connector"></a>Добавить соединитель

1. Щелкните значок **данных** на панели навигации слева. 
1. Разверните узел **соединители**.
1. Выберите **Azure обозреватель данных** в результирующих параметрах.

    :::image type="content" source="media/power-apps-connector/data-connectors-adx.png" alt-text="Создание нового подключения в Power Apps":::

Вы увидите новую область с именем **в приложении** с **Azure обозреватель данных** теперь включена.

   :::image type="content" source="media/power-apps-connector/adx-appears.png" alt-text="Создание нового подключения в Power Apps":::

### <a name="save-your-app"></a>Сохранение приложения

1. В строке меню выберите **файл** . 
1. Нажмите кнопку **сохранить** в области навигации слева.

    :::image type="content" source="media/power-apps-connector/save-app.png" alt-text="Создание нового подключения в Power Apps":::

1. Введите понятное имя для приложения. Нажмите кнопку **сохранить** в правом нижнем углу.

### <a name="advanced-settings"></a>Дополнительные параметры

1. В меню слева выберите **Параметры** .
1. Выберите **Дополнительные параметры**.
1. Выберите пункт **Динамическая схема** в результирующих параметрах. Включите эту функцию.

    :::image type="content" source="media/power-apps-connector/dynamic-schema.png" alt-text="Создание нового подключения в Power Apps":::

1. Найдите параметр **предельный размер строк данных для запросов, не делегируемых запросы** . Установите ограничение на возвращенные записи.

    :::image type="content" source="media/power-apps-connector/set-limit.png" alt-text="Создание нового подключения в Power Apps":::

    > [!NOTE]
    > Ограничение по умолчанию — 500, максимальное число возвращаемых записей — 2 000.

> [!IMPORTANT]
> Снова сохраните приложение и перезапустите его при необходимости.

### <a name="add-dropdown"></a>Добавить раскрывающийся список

1. В строке меню выберите **Вставить** . 
1. В открывшейся строке подменю выберите **входные данные** . 
1. Выберите **раскрывающийся** список в полученном раскрывающемся списке.
1. Щелкните вкладку **Дополнительно** в правой части popout.
1. Заполните поле ввода **элементов** следующим образом: ["Калифорния", "Мичигана"]

    :::image type="content" source="media/power-apps-connector/populate-dropdown.png" alt-text="Создание нового подключения в Power Apps" lightbox="media/power-apps-connector/populate-dropdown.png":::

1. Выполнив **раскрывающийся список** , выберите в раскрывающемся списке **Свойства** в строке формул параметр **onChange** .

1. Введите следующую формулу:

    ```kusto
    ClearCollect(
    Results,
    AzureDataExplorer.listKustoResultsPost(
    "https://help.kusto.windows.net",
    "Samples",
    "StormEvents | where State == '" & Dropdown1.SelectedText.Value & "' | take 15"
    ).value
    )
    ```
    
1. Нажмите кнопку **схема захвата** . Подождите время обработки.

    :::image type="content" source="media/power-apps-connector/capture-schema.png" alt-text="Создание нового подключения в Power Apps":::

### <a name="add-data-table"></a>Добавить таблицу данных

1. В строке меню выберите **Вставить** . 
1. В появившейся строке меню выберите **Таблица данных** .
1. Измените расположение таблицы данных и рассмотрите возможность добавления границы для видимости.
1. Выберите вкладку **Свойства** в правой части popout. Выберите результаты из раскрывающегося списка **источник данных** .
1. Щелкните ссылку **изменить поля** . 
1. Выберите **+ Добавить поле** в результирующем popout. 
    
    :::image type="content" source="media/power-apps-connector/insert-data-table-small.png" alt-text="Создание нового подключения в Power Apps" lightbox="media/power-apps-connector/insert-data-table.png":::

1. Выберите нужные поля и нажмите кнопку **Добавить** . Появится предварительный просмотр выбранной таблицы данных.

    :::image type="content" source="media/power-apps-connector/preview-table.png" alt-text="Создание нового подключения в Power Apps":::

### <a name="validate"></a>Проверить

1. Нажмите кнопку **Предварительный просмотр приложения** в правом верхнем углу экрана.
1. Попробуйте раскрывающийся список, прокрутите таблицу данных и убедитесь, что данные успешно извлечены и презентации.

    :::image type="content" source="media/power-apps-connector/preview-app.png" alt-text="Создание нового подключения в Power Apps":::

### <a name="limitations"></a>Ограничения

* В Power Apps можно получить до 2 000 записей результатов, возвращенных клиенту. Общая память для этих записей не может превышать 64 МБ и время выполнения семь минут.
* Соединитель не поддерживает операторы [ветвления](https://docs.microsoft.com/azure/data-explorer/kusto/query/forkoperator) и [аспекта](https://docs.microsoft.com/azure/data-explorer/kusto/query/facetoperator) .
* **Исключения времени ожидания**: ограничение времени ожидания соединителя равно 7 минутам. Чтобы избежать потенциальных проблем с временем ожидания, сделайте запрос более эффективным, чтобы он выполнялся быстрее, или разделите его на фрагменты. Каждый блок может выполняться в другой части запроса. Дополнительные сведения см. в разделе рекомендации по [запросам](https://docs.microsoft.com/azure/data-explorer/kusto/query/best-practices).

## <a name="next-steps"></a>Дальнейшие действия
