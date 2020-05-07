---
title: Визуализация данных из Azure обозреватель данных с помощью Sisense
description: Из этой статьи вы узнаете, как настроить обозреватель данных Azure в качестве источника данных для Sisense и визуализировать данные.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.custom: has-adal-ref
ms.date: 5/29/2019
ms.openlocfilehash: 4afbf9ddf53715b045dba46d03f233f1401ff770
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862162"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Визуализация данных из обозреватель данных Azure в Sisense

Sisense — это платформа аналитики бизнес-аналитики, которая позволяет создавать приложения аналитики, предоставляющие высокоинтерактивные пользовательские интерфейсы. Программное обеспечение для создания отчетов бизнес-аналитики и панели мониторинга позволяет получить доступ к данным и объединить их несколько раз. Вы можете подключаться к структурированным и неструктурированным источникам данных, объединять таблицы из нескольких источников с минимальным написанием скриптов и программировать и создавать интерактивные веб-панели мониторинга и отчеты. В этой статье вы узнаете, как настроить обозреватель данных Azure в качестве источника данных для Sisense и визуализировать данные из примера кластера.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этой статьи необходимо следующее:

* [Скачивание и установка приложения Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm)

* Создайте кластер и базу данных, включающую образец данных Стормевентс. Дополнительные сведения см. в разделе [Краткое руководство. Создание кластера и базы данных azure обозреватель данных](create-cluster-database-portal.md) и прием [демонстрационных данных в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Подключение к панелям мониторинга Sisense с помощью Azure обозреватель данных JDBC Connector

1. Скачайте и скопируйте последние версии следующих JAR-файлов в *. \Сисенсе\датаконнекторс\ждбкдриверс\адкс*

    * активатион-1.1. jar
    * adal4j-1.6.0. jar
    * Коммонс-кодек-1.10. jar
    * Commons-collections4-4.1. jar
    * Commons-lang3-3.5. jar
    * ГСОН-2.8.0. jar
    * жЦип-аннотатионс-1.0 -1. jar
    * жсон-смарт-1.3.1. jar
    * Ланг-Таг-1.4.4. jar
    * маил-1.4.7. jar
    * mssql-jdbc-7.2.1.jre8.jar
    * Нимбус-Жосе-ЖВТ-7.0.1. jar
    * OAuth2-oidc-SDK-5.24.1. jar
    * slf4j-API-1.7.21. jar

1. Откройте приложение **Sisense** .
1. Перейдите на вкладку **данные** и выберите **+ еластикубе** , чтобы создать новую модель еластикубе.

    ![Выбор Еластикубе](media/sisense/data-select-elasticube.png)

1. В поле **Добавить новую модель еластикубе**назовите модель Еластикубе и **Сохраните**.

    ![Добавить новую модель Еластикубе](media/sisense/add-new-elasticube-model.png)

1. Выберите **+ данные**.

    ![Кнопка "Выбор данных"](media/sisense/select-data.png)

1. На вкладке **Выбор соединителя** выберите **универсальный соединитель JDBC** .

    ![Выбор соединителя JDBC](media/sisense/select-connector.png)

1. На вкладке **Подключение** заполните следующие поля для **универсального соединителя JDBC** и нажмите кнопку **Далее**.

    ![Параметры соединителя JDBC](media/sisense/jdbc-connector.png)

    |Поле |Описание |
    |---------|---------|
    |Строка подключения     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Папка JAR JDBC  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Имя класса драйвера    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Имя пользователя   |    Имя пользователя AAD     |
    |Пароль     |   Пароль пользователя AAD      |

1. На вкладке **Выбор данных** найдите **пункт База данных** , чтобы выбрать соответствующую базу данных, к которой у вас есть разрешения. В этом примере выберите *test1*.

    ![Выбор базы данных](media/sisense/select-database.png)

1. В области *тест* (имя базы данных):
    1. Выберите имя таблицы для предварительного просмотра таблицы и просмотрите имена столбцов таблицы. Ненужные столбцы можно удалить.
    1. Установите флажок соответствующей таблицы, чтобы выбрать эту таблицу.
    1. Нажмите кнопку **Готово**.

    ![Выбор таблицы](media/sisense/select-table-see-columns.png)

1. Выберите **Сборка** , чтобы создать набор данных.

    * В окне **Сборка** выберите **Сборка**.

      ![Окно сборки](media/sisense/build-window.png)

    * Дождитесь завершения процесса сборки и выберите **Сборка выполнена**.

      ![Сборка успешно завершена](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Создание панелей мониторинга Sisense

1. На вкладке **аналитика** выберите **+**  >  **создать панель мониторинга** , чтобы создать панели мониторинга для этого набора данных.

    ![Новая панель мониторинга](media/sisense/new-dashboard.png)

1. Выберите панель мониторинга и щелкните **создать**.

    ![Создание панели мониторинга](media/sisense/create-dashboard.png)

1. В разделе **новое мини**-приложение выберите **+ выбрать данные** , чтобы создать новое мини-приложение.

    ![Добавление полей на панель мониторинга Стормевентс](media/sisense/storm-dashboard-add-field.png)

1. Выберите **+ добавить дополнительные данные** , чтобы добавить дополнительные столбцы в граф.

    ![Добавление данных в Graph](media/sisense/add-more-data.png)

1. Выберите **+ мини** -приложение, чтобы создать другое мини-приложение. Перетащите мини-приложения, чтобы изменить расположение панели мониторинга.

    ![Панель мониторинга Storm](media/sisense/final-dashboard.png)

Теперь вы можете исследовать данные с помощью Visual Analytics, создавать дополнительные панели мониторинга и преобразовывать данные в ценные сведения, чтобы повлиять на бизнес.

## <a name="next-steps"></a>Дальнейшие действия

* [Написание запросов для обозревателя данных Azure](write-queries.md)