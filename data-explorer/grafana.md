---
title: Визуализация данных от Azure Data Explorer с помощью Grafana
description: В этой статье вы научитесь настраивать Azure Data Explorer в качестве источника данных для Grafana, а затем визуализировать данные из выборочного кластера.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: d58e557ecbd7e51e0295bf1a8ece3aaa4996da47
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500155"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Визуализация данных из Azure Data Explorer в Grafana

Аналитическая платформа Grafana позволяет запрашивать и визуализировать данные, а также создавать и совместно использовать панели мониторинга, основанные на этих визуализациях. Grafana предоставляет *подключаемый модуль* Azure Data Explorer, который позволяет подключиться к службе Azure Data Explorer для визуализации данных. В этой статье вы научитесь настраивать Azure Data Explorer в качестве источника данных для Grafana, а затем визуализировать данные из выборочного кластера.

Используйте следующее видео, чтобы узнать, как использовать плагин Azure Data Explorer Grafana, настроить Azure Data Explorer в качестве источника данных для Grafana, а затем визуализировать данные. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Кроме того, можно [настроить источник данных](#configure-the-data-source) и [визуализировать данные,](#visualize-data) описанные в статье ниже.

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи необходимо выполнить следующее:

* [Grafana 5.3.0 или более поздней версии](https://docs.grafana.org/installation/) для вашей операционной системы.

* [Подключаемый модуль Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) для Grafana.

* Кластер с демонстрационными данными StormEvents. Дополнительные сведения см. в разделах [Краткое руководство. Создание кластера и базы данных обозревателя данных Azure](create-cluster-database-portal.md) и [Передача данных примера в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Настройка свойств и тестирование подключения

Теперь, когда вы назначили субъекту-службе роль *читателя*, можно настроить свойства для экземпляра Grafana и проверить подключение в Azure Data Explorer.

1. Откройте Grafana и щелкните значок шестеренки в меню слева, затем выберите **Data Sources** (Источники данных).

    ![Источники данных](media/grafana/data-sources.png)

1. Выберите **Add data source**(Добавить источник данных).

1. На странице **Data Sources / New** (Источники данных / Создать) введите имя для источника данных и выберите тип **Azure Data Explorer Datasource** (Источник данных Azure Data Explorer).

    ![Имя и тип подключения](media/grafana/connection-name-type.png)

1. Введите имя кластера в формате https://{имя_кластера}.{регион}.kusto.windows.net. Введите остальные значения, полученные с помощью портала Azure или интерфейса командной строки. Сопоставление параметров см. в таблице ниже.

    ![Свойства подключения](media/grafana/connection-properties.png)

    | Пользовательский интерфейс Grafana | Портал Azure | Azure CLI |
    | --- | --- | --- |
    | идентификатор подписки; | Идентификатор подписки | SubscriptionId |
    | Идентификатор клиента | Идентификатор каталога | tenant |
    | Идентификатор клиента | Идентификатор приложения | appId |
    | Секрет клиента | Пароль | password |
    | | | |

1. Щелкните **Save & Test** (Сохранить и протестировать).

    Если тест завершится успешно, переходите к следующему разделу. Если вы сталкивались с какими-либо проблемами, проверьте значения, указанные в Grafana, и просмотрите предыдущие шаги.

## <a name="visualize-data"></a>Визуализируйте данные

Итак, вы завершили настройку Azure Data Explorer в качестве источника данных для Grafana, и теперь пора визуализировать данные. Мы покажем здесь простой пример, но возможностей намного больше. Рекомендуем просмотреть примеры других запросов к примеру набора данных в статье [Написание запросов для обозревателя данных Azure](write-queries.md).

1. Откройте Grafana и щелкните значок плюса в меню слева, затем выберите **Dashboard** (Панель мониторинга).

    ![Создание панели мониторинга](media/grafana/create-dashboard.png)

1. На вкладке **Add** (Добавить) выберите **Graph** (График).

    ![Добавление графика](media/grafana/add-graph.png)

1. На панели графика щелкните **Panel Title** (Заголовок панели), а затем **Edit** (Редактировать).

    ![Изменение панели](media/grafana/edit-panel.png)

1. В нижней части панели щелкните **Data Source** (Источник данных) и выберите только что настроенный источник данных.

    ![Выберите источник данных](media/grafana/select-data-source.png)

1. Скопируйте приведенный ниже запрос, вставьте его в область запроса и щелкните **Run** (Запуск). Этот запрос возвращает число событий из примера набора данных в разбивке по дням.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Выполнение запроса](media/grafana/run-query.png)

1. На этом графике не отображаются никакие результаты, так как по умолчанию область действия ограничивается шестью последними часами. В меню вверху выберите **Last 6 hours** (Последние 6 часов).

    ![Последние шесть часов](media/grafana/last-six-hours.png)

1. Укажите настраиваемый диапазон, куда входит 2007 год, данные за который представлены в примере набора данных StormEvents. Нажмите кнопку **Применить**.

    ![Настраиваемый диапазон дат](media/grafana/custom-date-range.png)

    Теперь на графике отображаются данные за 2007 год по дням.

    ![Готовый график](media/grafana/finished-graph.png)

1. В меню вверху щелкните значок сохранения. ![Значок "Сохранить"](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Создание предупреждений

1. В домашней панели мониторинга выберите**каналы** **оповещения** > для создания нового канала уведомлений

    ![создание канала уведомлений](media/grafana/create-notification-channel.png)

1. Создайте новый **канал уведомления,** затем **сохраните**.

    ![Создание нового канала уведомлений](media/grafana/new-notification-channel-adx.png)

1. На **панели мониторинга**выберите **отсеив.**

    ![выбрать отображее на приборной панели](media/grafana/edit-panel-4-alert.png)

1. Выберите значок оповещения колокола, чтобы открыть панель **оповещения.** Выберите **Создать оповещение**. Выполните следующие свойства в панели **оповещения.**

    ![Свойства оповещения](media/grafana/alert-properties.png)

1. Выберите значок **панели мониторинга Сохранить,** чтобы сохранить изменения.

## <a name="next-steps"></a>Следующие шаги

* [Написание запросов для обозревателя данных Azure](write-queries.md)