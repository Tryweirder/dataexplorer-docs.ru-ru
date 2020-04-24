---
title: Визуализация данных с помощью запроса Azure Data Explorer Kusto, импортируемого в Microsoft Excel
description: В этой статье вы узнаете, как импортировать запрос Azure Data Explorer Kusto в Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499531"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Визуализация данных с помощью запроса Azure Data Explorer Kusto, импортируемого в Microsoft Excel

Azure Data Explorer предоставляет два варианта подключения к данным в Excel: использовать родной разъем или импортировать запрос из Azure Data Explorer. В этой статье показано, как импортировать запрос из Azure Data Explorer в Excel для визуализации данных. Добавьте запрос Kusto в качестве источника данных Excel для дополнительных вычислений или визуализаций данных.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Организационная учетная запись электронной почты, которая является членом каталога Azure Active, чтобы можно было подключиться к [группе помощи Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>или диспетчер конфигурации служб</br>
* Создайте [кластер тестирования и базу данных](create-cluster-database-portal.md) и вопийте в [приложении Web-uI Azure Data Explorer.](https://dataexplorer.azure.com/)

## <a name="define-kusto-query-as-an-excel-data-source"></a>Определение запроса Kusto как источника данных Excel

1. В [веб-мине Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples)задвавайте запрос и проверяйте результаты.

1. Выберите вкладку **«Поделиться»** и выберите **запрос на Power BI.**

    ![Веб-запрос на запрос для Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Появляется окно со следующим уведомлением:

    ![экспортный запрос в буфер обмена](media/excel-blank-query/query-exported-to-clipboard.png)

1. Открыть **Microsoft Excel**.

1. Во вкладке **«Данные»** выберите **«Получить данные** > **из других источников** > **пустого запроса».**

    ![Получить данные и выбрать пустой запрос](media/excel-blank-query/get-data-blank-query.png)

1. Открывается окно **редактора энерго-запроса.** В окне выберите **Расширенный редактор**.

    ![Окно редактора запроса питания](media/excel-blank-query/power-query-editor.png)

1. В окне **расширенного редактора** вставьте запрос, который вы экспортируете в буфер обмена, и выберите **Done.**

    ![Расширенный запрос редактора](media/excel-blank-query/advanced-editor-query.png)    

1. Для проверки подлинности выберите **учетные данные Edit.**

    ![Изменение учетных данных](media/excel-blank-query/edit-credentials.png)

1. Выберите **организационную учетную запись** и **вопийте.** Завершите процесс вхинга, а затем выберите **Connect.**

    ![Полный входин](media/excel-blank-query/complete-sign-in.png)

    Повторите предыдущие шаги, чтобы добавить больше запросов. Можно переименовать запросы в более значимые имена.

1. Выберите кнопку **"Закрыть &** загрузки", чтобы получить данные в Excel.

    ![Выберите близко и загрузить](media/excel-blank-query/close-and-load.png)

1. Теперь ваши данные в Excel. Выберите кнопку **Обновления,** чтобы обновить запрос.

    ![Просмотр данных в Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Следующие шаги

[Визуализация данных с помощью разъема Azure Data Explorer для Excel](excel-connector.md)