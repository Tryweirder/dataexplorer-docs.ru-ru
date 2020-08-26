---
title: Визуализация данных из обозреватель данных Azure с помощью импортированного запроса Power BI
description: 'В этой статье вы узнаете, как использовать один из трех вариантов визуализации данных в Power BI: импорт запроса из обозреватель данных Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 4ab51eaef43596da0f2b511a4a617afaade939cf
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875078"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Визуализация данных с помощью запроса, импортированного в Power BI

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Power BI — это решение бизнес-аналитики, позволяющее визуализировать данные и делиться результатами с коллегами.

Azure Data Explorer поддерживает три варианта подключения к данным в Power BI: использование встроенного соединителя, импорт запроса из Azure Data Explorer и использование SQL-запроса. В этой статье показано, как импортировать запрос, чтобы получить данные и визуализировать его в Power BI отчете.

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этой статьи необходимо следующее:

* учетная запись электронной почты организации, подключенной к Azure Active Directory, для доступа к [кластеру help в Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples);

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (выберите **Скачать бесплатно**);

* [классическое приложение Azure Data Explorer.](kusto/tools/kusto-explorer.md)

## <a name="get-data-from-azure-data-explorer"></a>Получение данных из Azure Data Explorer

Сначала вам нужно создать запрос в классическом приложении Azure Data Explorer и экспортировать его в Power BI. Затем вы подключаетесь к кластеру help в Azure Data Explorer, чтобы добавить набор данных из таблицы *StormEvents*. [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

1. В браузере перейдите к, [https://help.kusto.windows.net/](https://help.kusto.windows.net/) чтобы запустить классическое приложение Azure обозреватель данных.

1. В приложении скопируйте следующий запрос в окно запроса в правом верхнем углу и запустите его.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    Первые несколько строк результирующего набора должны выглядеть, как на рисунке ниже.

    ![Результаты запроса](media/power-bi-imported-query/query-results.png)

1. На вкладке **Tools** (Средства) выберите **Query to Power BI** (Создать запрос в Power BI) и щелкните **ОК**.

    ![Экспорт запроса](media/power-bi-imported-query/export-query.png)

1. В Power BI Desktop на вкладке **Главная** выберите **Получить данные**, а затем **Пустой запрос**.

    ![Получение данных](media/power-bi-imported-query/get-data.png)

1. В редакторе Power Query на вкладке **Главная** выберите **Расширенный редактор**.

1. В окне **Расширенный редактор** вставьте экспортированный запрос и нажмите **Готово**.

    ![Вставка запроса](media/power-bi-imported-query/paste-query.png)

1. В главном окне редактора Power Query выберите **Изменить учетные данные**. Выберите **Учетная запись организации**, выполните вход и нажмите **Подключиться**.

    ![Изменить учетные данные](media/power-bi-imported-query/edit-credentials.png)

1. На вкладке **Главная** выберите **Закрыть и применить**.

    ![Закрыть и применить](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Визуализация данных в отчете

[!INCLUDE [data-explorer-power-bi-visualize-basic](includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если отчет, созданный для этой статьи, больше не нужен, удалите Power BI Desktop (pbix-файл).

## <a name="next-steps"></a>Дальнейшие шаги

[Визуализация данных с помощью соединителя Azure Data Explorer для Power BI](power-bi-connector.md)