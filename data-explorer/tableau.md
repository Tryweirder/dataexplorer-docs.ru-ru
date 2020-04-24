---
title: Используйте разъем ODBC Explorer данных Azure Data Explorer для визуализации данных Tableau
description: В этой статье вы узнаете, как использовать подключение к открытой базе данных (ODBC) к подключению Azure Data Explorer для визуализации данных с помощью Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5fc98a0bb173bc926dc289b16c1aa8d1a0f5d9de
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501130"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Визуализация данных от Azure Data Explorer в Tableau

 [Tableau](https://www.tableau.com/) является визуальной аналитики платформы для бизнес-аналитики. Чтобы подключиться к Azure Data Explorer из Tableau и принести данные из выборочного кластера, используйте драйвер s'L Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи необходимо выполнить следующее:

* [Подключайтесь к Azure Data Explorer с помощью ODBC](connect-odbc.md) с помощью драйвера S'L Server ODBC, чтобы подключиться к Azure Data Explorer из Таблицау. 

* Таблица Настольная, полная или [пробная](https://www.tableau.com/products/desktop/download) версия.

* Кластер с демонстрационными данными StormEvents. Для получения дополнительной информации смотрите [Создать кластер и базу данных Azure Data Explorer и](create-cluster-database-portal.md) [выборочные данные в Azure Data Explorer.](ingest-sample-data.md)

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Визуализация данных в Tableau 

После того как вы закончите настройку ODBC, вы можете принести выборочные данные в Tableau.

1. В Tableau Desktop, в левом меню, выберите **другие базы данных (ODBC).**

    ![Подключение с помощью ODBC](media/tableau/connect-odbc.png)

1. Для **DSN**выберите источник данных, созданный для ODBC, а затем выберите **«Войти в».**

    ![Ввод в систему ODBC](media/tableau/odbc-sign-in.png)

1. Для **базы данных**выберите базу данных в группе выборки, например *TestDatabase.* Для **Schema**, выберите *dbo*, и для **таблицы**, выберите таблицу образца *StormEvents.*

    ![Выберите базу данных и таблицу](media/tableau/select-database-table.png)

1. Теперь Tableau показывает схему для выборочных данных. Выберите **обновление сейчас,** чтобы принести данные в Tableau.

    ![Обновление данных](media/tableau/update-data.png)

    При импорте данных Tableau показывает строки данных, аналогичные следующему изображению.

    ![Результирующий набор](media/tableau/result-set.png)

1. Теперь можно создавать визуализации в Tableau на основе данных, полученных от Azure Data Explorer. Для получения дополнительной информации [см.](https://www.tableau.com/learn)

## <a name="next-steps"></a>Следующие шаги

* [Написание запросов для обозревателя данных Azure](write-queries.md)