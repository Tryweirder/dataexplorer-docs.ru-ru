---
title: Визуализация данных от Azure Data Explorer с помощью Sisense
description: В этой статье узнайте, как настроить Azure Data Explorer в качестве источника данных для Sisense, и визуализировать данные.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 33a3e51e8bd892a5d4318445ff8e5f9ee26c4b40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501195"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Визуализация данных от Azure Data Explorer в Sisense

Sisense — это аналитическая платформа бизнес-аналитики, которая позволяет создавать аналитические приложения, обеспечивающие высокое взаимодействие с пользователями. Программное обеспечение для представления отчетов бизнес-аналитики и панели мониторинга позволяет получать доступ и комбинировать данные в несколько кликов. Можно подключиться к структурированным и неструктурированным источникам данных, объединить таблицы из нескольких источников с минимальными сценариями и кодированием, а также создать интерактивные веб-панели мониторинга и отчеты. В этой статье вы узнаете, как настроить Azure Data Explorer в качестве источника данных для Sisense, и визуализировать данные из кластера выборки.

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи необходимо выполнить следующее:

* [Скачать и установить приложение Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Создайте кластер и базу данных, включающие выборочные данные StormEvents. Для получения дополнительной информации [Ingest sample data into Azure Data Explorer](ingest-sample-data.md)см. [Quickstart: Create an Azure Data Explorer cluster and database](create-cluster-database-portal.md)

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Подключение к панелям мониторинга Sisense с помощью разъема Azure Data Explorer JDBC

1. Скачать и скопировать последние версии следующих файлов банку *.. «Sisense»DataConnectors,jdbcdrivers* 

    * активация-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * Commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-аннотации-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * почта-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * нимбус-джоз-джвт-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Открытое приложение **Sisense.**
1. Выберите вкладку **Data** и выберите **elastiCube** для создания новой модели ElastiCube.
    
    ![Выберите ElastiCube](media/sisense/data-select-elasticube.png)

1. В **Добавить новую модель ElastiCube**, назовите модель ElastiCube и **сохранить**.
   
    ![Добавить новую модель ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Выберите **данные**.

    ![Выберите кнопку данных](media/sisense/select-data.png)

1. Во вкладке **Select Connector** выберите **разъем Generic JDBC.**

    ![Выберите разъем JDBC](media/sisense/select-connector.png)

1. Во вкладке **Connect** выполните следующие поля для **разъема Generic JDBC** и выберите **Следующий.**

    ![Настройки разъема JDBC](media/sisense/jdbc-connector.png)

    |Поле |Описание |
    |---------|---------|
    |Строка подключения     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Папка JDBC JARs  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Имя водителя класса    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Имя пользователя   |    Имя пользователя AAD     |
    |Пароль     |   Пароль пользователя AAD      |

1. Во вкладке **Select Data** **выберите базу данных,** чтобы выбрать соответствующую базу данных, на которую у вас есть разрешения. В этом примере выберите *test1.*

    ![Выбор базы данных](media/sisense/select-database.png)

1. В *тестовом* (имя базы данных) панель:
    1. Выберите имя таблицы для предварительного просмотра таблицы и просмотреименназваний столбца таблицы. Можно удалить ненужные столбцы.
    1. Выберите флажок соответствующей таблицы, чтобы выбрать эту таблицу. 
    1. Нажмите кнопку **Готово**.

    ![Выбор таблицы](media/sisense/select-table-see-columns.png)    

1. Выберите **сборку** для создания набора данных. 

    * В окне **сборки** выберите **Build**.

      ![Окно сборки](media/sisense/build-window.png)

    * Подождите, пока процесс сборки не будет завершен, а затем выберите **Build Succeeded.**

      ![Сборка успешно завершена](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Создание панелей мониторинга Sisense

1. Во вкладке **+**  >  **«Аналитика»** выберите **новую панель мониторинга** для создания панелей мониторинга на этом наборе данных.

    ![Новая панель мониторинга](media/sisense/new-dashboard.png)

1. Выберите панель мониторинга и выберите **Создать**. 

    ![Создание панели мониторинга](media/sisense/create-dashboard.png)

1. В рамках **New Widget**выберите **данные для** создания нового виджета. 

    ![Добавление полей в панель мониторинга StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Выберите **и добавьте дополнительные данные,** чтобы добавить дополнительные столбцы на график. 

    ![Добавление дополнительных данных в график](media/sisense/add-more-data.png)

1. Выберите **виджет** для создания другого виджета. Перетащите и бросьте виджеты, чтобы переставить приборную панель.

    ![Панель мониторинга Storm](media/sisense/final-dashboard.png)

Теперь вы можете изучить свои данные с помощью визуальной аналитики, создать дополнительные панели мониторинга и преобразовать данные в действенные сведения, чтобы повлиять на ваш бизнес.

## <a name="next-steps"></a>Следующие шаги

* [Написание запросов для обозревателя данных Azure](write-queries.md)

