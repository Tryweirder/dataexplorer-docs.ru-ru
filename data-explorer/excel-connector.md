---
title: Визуализация данных с помощью разъема Azure Data Explorer для Microsoft Excel
description: В этой статье вы узнаете, как использовать разъем Excel для Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499687"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Визуализация данных с помощью разъема Azure Data Explorer для Excel

Azure Data Explorer предоставляет два варианта подключения к данным в Excel: использовать родной разъем или импортировать запрос из Azure Data Explorer. В этой статье показано, как использовать родной разъем в Excel и подключиться к кластеру Azure Data Explorer для получения и визуализации данных.

Родной разъем Azure Data Explorer Excel предлагает возможность экспортировать результаты запросов в Excel. Вы также можете добавить запрос КЗЛ в качестве источника данных Excel для дополнительных вычислений или визуализаций.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Определите запрос Kusto как источник данных Excel и загрузите данные в Excel

1. Открыть **Microsoft Excel**.
1. Во вкладке **Данные** выберите **«Получить данные** > **из Azure»** > **из Azure Data Explorer.**

    ![Получение данных из Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. В окне **Azure Data Explorer (Kusto)** выполните следующие поля и выберите **OK.**

    ![Окно Azure Data Explorer (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Поле   |Описание |
    |---------|---------|
    |**Кластер**   |   Название кластера (обязательно)      |    
    |**База данных**     |    Имя базы данных      |    
    |**Имя таблицы или запрос Azure Data Explorer**    |     Название таблицы или запрос Azure Data Explorer    | 
    
    **Расширенные опции:**

     |Поле   |Описание |
    |---------|---------|
    |**Ограничьте число записей результатов запроса**     |     Ограничьте количество записей, загруженных в Excel  |    
    |**Ограничьте размер данных результата запроса (байты)**    |    Ограничьте размер данных      |   
    |**Усечение отсутствовательных результатов**    |         |      
    |**Дополнительные инструкции set (разделенные запятыми)**    |    Добавление `set` инструкций для применения к источнику данных     |   

1.  В панели **Навигатор** перейдите к правильной таблице. В панели предварительного просмотра таблицы таблицы выберите **Преобразование данных** для внесения изменений в данные или выберите **Загрузку** для загрузки в Excel.

![Окно предварительного просмотра таблицы](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Если имя **базы данных** и/или **название таблицы или запрос Azure Data Explorer** уже указаны, правильное панель предварительного просмотра таблицы откроется автоматически. 

## <a name="analyze-and-visualize-data-in-excel"></a>Анализ и визуализация данных в Excel

После того, как данные загружаются, чтобы преуспеть и доступна в листе Excel, вы можете анализировать, суммировать и визуализировать данные, создавая отношения и визуальные эффекты. 

1.  Во вкладке **«Дизайн таблицы»** выберите **«Суммировать» с помощью PivotTable**. В окне **Create PivotTable** выберите соответствующую таблицу и выберите **OK**.

    ![Создание таблицы поворота](media/excel-connector/create-pivot-table.png)

1. В панели **PivotTable Fields** выберите соответствующие столбцы таблиц для создания сводных таблиц. В приведенном ниже примере выбираются **EventId** и **State.**
    
    ![Выберите поля PivotTable](media/excel-connector/pivot-table-pick-fields.png)

1. Во вкладке **PivotTable Analyse** выберите **PivotChart** для создания визуальных элементов на основе таблицы. 

    ![Диаграмма поворота](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. В приведенном ниже примере используйте **Event Id,** **StartTime**и **EventType** для просмотра дополнительной информации о погодных явлениях.

    ![Визуализируйте данные](media/excel-connector/visualize-excel-data.png)

1. Создайте полные панели мониторинга данных.

## <a name="next-steps"></a>Следующие шаги

[Визуализация данных с помощью запроса Azure Data Explorer Kusto, импортируемого в Microsoft Excel](excel-blank-query.md)