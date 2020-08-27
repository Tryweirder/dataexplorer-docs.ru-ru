---
title: Визуализация данных с помощью запроса Azure обозреватель данных Kusto, импортированного в Microsoft Excel
description: Из этой статьи вы узнаете, как импортировать запрос Azure обозреватель данных Kusto в Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 0c1900b1feb0af150e25fbc85c231f31a30663b0
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874636"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Визуализация данных с помощью запроса Azure обозреватель данных Kusto, импортированного в Microsoft Excel

Azure обозреватель данных предоставляет два варианта подключения к данным в Excel: Используйте собственный соединитель или импортируйте запрос из обозреватель данных Azure. В этой статье показано, как импортировать запрос из обозреватель данных Azure в Excel для визуализации данных. Добавьте запрос Kusto в качестве источника данных Excel для выполнения дополнительных вычислений или визуализаций данных.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Учетная запись электронной почты организации, которая является членом Azure Active Directory, поэтому вы можете подключиться к [кластеру справки azure обозреватель данных](https://dataexplorer.azure.com/clusters/help/databases/Samples) . 
<br>или диспетчер конфигурации служб</br>
* Создайте [тестовый кластер и базу данных](create-cluster-database-portal.md) и войдите в [приложение пользовательского веб-интерфейса Azure обозреватель данных](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Определение запроса Kusto в качестве источника данных Excel

1. В [пользовательском веб-интерфейсе Azure обозреватель данных](https://dataexplorer.azure.com/clusters/help/databases/Samples)выполните запрос и проверьте результаты.

1. Перейдите на вкладку **общий доступ** и выберите **запрос для Power BI**.

    ![Запрос веб-интерфейса для Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Появится окно со следующим уведомлением:

    ![экспортировать запрос в буфер обмена](media/excel-blank-query/query-exported-to-clipboard.png)

1. Откройте **Microsoft Excel**.

1. На вкладке **данные** выберите **получить данные**  >  **из других источников**  >  **пустой запрос**.

    ![Получение данных и выбор пустого запроса](media/excel-blank-query/get-data-blank-query.png)

1. Откроется окно **редактора Power Query** . В окне выберите **Расширенный редактор**.

    ![Окно редактора Power Query](media/excel-blank-query/power-query-editor.png)

1. В окне **Расширенный редактор** вставьте запрос, экспортированный в буфер обмена, и нажмите кнопку **Готово**.

    ![Расширенный редактор запросов](media/excel-blank-query/advanced-editor-query.png)    

1. Для проверки подлинности выберите **изменить учетные данные**.

    ![Изменить учетные данные](media/excel-blank-query/edit-credentials.png)

1. Выберите **учетную запись организации** и **Войдите в нее**. Завершите процесс входа и нажмите кнопку **подключить**.

    ![Завершение входа](media/excel-blank-query/complete-sign-in.png)

    Повторите предыдущие шаги, чтобы добавить дополнительные запросы. Можно переименовать запросы на более значимые имена.

1. Нажмите кнопку **закрыть & загрузить** , чтобы получить данные в Excel.

    ![Выберите Закрыть и загрузить](media/excel-blank-query/close-and-load.png)

1. Теперь данные находятся в Excel. Нажмите кнопку **Обновить** , чтобы обновить запрос.

    ![Просмотр данных в Excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Дальнейшие действия

[Визуализация данных с помощью соединителя Azure обозреватель данных для Excel](excel-connector.md)