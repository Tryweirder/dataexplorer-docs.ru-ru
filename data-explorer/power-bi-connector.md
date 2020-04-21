---
title: Визуализация данных с разъемом Azure Data Explorer для Power BI
description: 'В этой статье вы узнаете, как использовать один из трех вариантов визуализации данных в Power BI: разъем Power BI для Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d162adec8ea0c5244fef601bf409d12432f4ce00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502001"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Визуализация данных с помощью соединителя Azure Data Explorer для Power BI

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Power BI — это решение бизнес-аналитики, позволяющее визуализировать данные и делиться результатами с коллегами. Azure Data Explorer поддерживает три варианта подключения к данным в Power BI: использование встроенного соединителя, импорт запроса из Azure Data Explorer и использование SQL-запроса. В этой статье показано, как использовать встроенный разъем для получения данных и визуализации их в отчете Power BI. Использование нативного разъема Azure Data Explorer для создания панелей мониторинга Power BI является простым. Разъем Power BI поддерживает [режимы подключения к импорту и прямому запросу.](https://docs.microsoft.com/power-bi/desktop-directquery-about) Панели мониторинга можно создавать с помощью режима **импорта** или **прямого квирера** в зависимости от сценария, масштаба и требований к производительности. 

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи необходимо выполнить следующее:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* учетная запись электронной почты организации, подключенной к Azure Active Directory, для доступа к [кластеру help в Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples);
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (выберите **Скачать бесплатно**);

## <a name="get-data-from-azure-data-explorer"></a>Получение данных из Azure Data Explorer

Сначала подключитесь к кластеру help Azure Data Explorer, а затем добавьте часть данных из таблицы *StormEvents*. [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

1. В Power BI Desktop на вкладке **Главная** выберите **Получение данных**, а затем — **Дополнительно**.

    ![Получение данных](media/power-bi-connector/get-data-more.png)

1. Поиск *для Исследователя данных Azure,* выберите **Azure Data Explorer,** затем **подключитесь.**

    ![Поиск и получение данных](media/power-bi-connector/search-get-data.png)

1. На экране **Azure Data Explorer (Kusto)** заполните форму следующей информацией.

    ![Кластер, база данных, параметры таблицы](media/power-bi-connector/cluster-database-table.png)

    **Параметр** | **Значение** | **Описание поля**
    |---|---|---|
    | Кластер | *https://help.kusto.windows.net* | URL-адрес кластера help. Для других кластеров URL-адрес находится в форме *https://\<ClusterName\>.\< Регион\>.kusto.windows.net*. |
    | База данных | Не указывайте | База данных, размещенная в кластере, к которому вы подключаетесь. Это будет указано позже. |
    | Имя таблицы | Не указывайте | Одна из таблиц в базе данных или запрос, например <code>StormEvents \| take 1000</code>. Это будет указано позже. |
    | Расширенные параметры | Не указывайте | Варианты запроса, например размер результирующего набора. |
    | Режим подключения к данным | *DirectQuery* | Определяет, должно ли решение Power BI импортировать данные или подключаться непосредственно к источнику данных. С этим соединителем можно использовать любой вариант. |
    | | | |
    
    > [!NOTE]
    > В режиме **импорта** данные перемещались в Power BI. В режиме **Direct'ery** данные запрашиваются непосредственно из кластера Azure Data Explorer.
    >
    > Используйте режим **импорта,** когда:
    > * Набор данных невелик.
    > * Вам не нужны данные в режиме реального времени. 
    > * Ваши данные уже агрегированы или вы выполняете [агрегацию в Kusto](kusto/query/summarizeoperator.md#list-of-aggregation-functions)    
    >
    > Используйте режим **Прямого кватерия,** когда:
    > * Набор данных очень большой. 
    > * Вам нужны данные в режиме реального времени.   

1. Если вы еще не установили подключение к кластеру help, выполните вход. Войдите с помощью учетной записи организации и выберите **Подключение**.

    ![Войти](media/power-bi-connector/sign-in.png)

1. На экране **Навигатор** разверните базу данных **Примеры**, выберите **StormEvents**, а затем **Изменить**.

    ![Выбор таблицы](media/power-bi-connector/select-table.png)

    Таблица откроется в редакторе запросов Power, где можно изменить строки и столбцы перед импортом данных.

1. В редакторе Power Query щелкните стрелку рядом со столбцом **DamageCrops** и выберите **Сортировка по убыванию**.

    ![Сортировка по убыванию для DamageCrops](media/power-bi-connector/sort-descending.png)

1. На вкладке **Главная** выберите **Сохранить строки**, а затем **Сохранить верхние строки**. Введите значение *1000* для сохранения 1000 верхних строк отсортированной таблицы.

    ![Сохранение верхних строк](media/power-bi-connector/keep-top-rows.png)

1. На вкладке **Главная** выберите **Закрыть и применить**.

    ![Закрыть и применить](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Визуализация данных в отчете

[!INCLUDE [data-explorer-power-bi-visualize-basic](includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужен отчет, созданный для этой статьи, удалите файл Power BI Desktop (.pbix).

## <a name="next-steps"></a>Следующие шаги

[Советы по использованию разъема Azure Data Explorer для Power BI для запроса данных](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
