---
title: Подключение к исследователю данных Azure с помощью ODBC
description: В этой статье вы узнаете, как настроить подключение к открытой базе данных (ODBC) к исследователю данных Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 156257613195cb53730273ac3a654908feac9601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496619"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Подключение к исследователю данных Azure с помощью ODBC

Open Database Connectivity[(ODBC](/sql/odbc/reference/odbc-overview)) — это широко признанный интерфейс программирования приложений (API) для доступа к базе данных. Используйте ODBC для подключения к Azure Data Explorer из приложений, не имеющие выделенного разъема.

За кулисами приложения вызывают функции в интерфейсе ODBC, которые реализуются в модулях, называемых *драйверами*для баз данных. Azure Data Explorer поддерживает подмножество протокола связи сервера S'L Server[(MS-TDS),](kusto/api/tds/index.md)чтобы он мог использовать драйвер ODBC для сервера S'L.

Используя следующее видео, вы можете научиться создавать соединение ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Кроме того, можно [настроить источник данных ODBC,](#configure-the-odbc-data-source) как описано ниже. 

В статье вы узнаете, как использовать драйвер ODBC сервера S'L, так что вы можете подключиться к Azure Data Explorer из любого приложения, которое поддерживает ODBC. 

## <a name="prerequisites"></a>Предварительные требования

Кроме этого, вам потребуются:

* [Драйвер Microsoft ODBC для версии S'L Server 17.2.0.1 или позже](/sql/connect/odbc/download-odbc-driver-for-sql-server) для вашей операционной системы.

## <a name="configure-the-odbc-data-source"></a>Настройка источника данных ODBC

Выполните следующие действия, чтобы настроить источник данных ODBC с помощью драйвера ODBC для сервера S'L.

1. В Windows ищите *источники данных ODBC*и открываем настольное приложение ODBC Data Sources.

1. Выберите **Добавить**.

    ![Добавление источника данных](media/connect-odbc/add-data-source.png)

1. Выберите **ODBC Driver 17 для сервера S'L,** а затем **закончите.**

    ![Выберите драйвер](media/connect-odbc/select-driver.png)

1. Введите имя и описание соединения и кластера, к которому вы хотите подключиться, а затем выберите **Следующий.** URL кластера должен быть в форме * \<ClusterName\>.\< Регион\>.kusto.windows.net*.

    ![Выбор сервера](media/connect-odbc/select-server.png)

1. Выберите **Активный каталог Интегрированный** затем **следующий**.

    ![Встроенная проверка подлинности Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Выберите базу данных с образцами данных, а затем **далее**.

    ![Изменение базы данных по умолчанию](media/connect-odbc/change-default-database.png)

1. На следующем экране оставьте все параметры по умолчанию, а затем выберите **Finish.**

1. Выберите **источник тестовых данных**.

    ![Источник данных тестирования](media/connect-odbc/test-data-source.png)

1. Убедитесь, что тест удалось, а затем выбрать **OK**. Если тест не был успешным, проверьте значения, указанные в предыдущих шагах, и убедитесь, что у вас достаточно разрешений для подключения к кластеру.

    ![Тест удалось](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Следующие шаги

* [Подключение к исследователю данных Azure из таблицы](tableau.md)