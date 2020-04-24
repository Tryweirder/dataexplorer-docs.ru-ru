---
title: Примеры использования разъема потока исследователя данных Microsoft Azure (Preview)
description: Изучите несколько общих примеров использования разъема Microsoft Flow.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 05ff0e41347d56cc1e204defa8d5f250a881c84f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498959"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Примеры использования разъема потока Microsoft Flow (Preview)

Разъем потока Azure Data Explorer позволяет Azure Data Explorer использовать [возможности потока Microsoft Power Automate](https://flow.microsoft.com/) для автоматического выполнения запросов и команд Kusto в рамках запланированной или срабатывающей задачи. Этот документ содержит несколько общих примеров использования разъема Microsoft Flow.

Для получения дополнительной [Microsoft Flow connector (Preview)](flow.md)информации см.

* [Разъем и сот](#microsoft-flow-connector-and-sql)
* [Нажмите данные на набор данных Power BI](#push-data-to-power-bi-dataset)
* [Условные запросы](#conditional-queries)
* [Отправьте по электронной почте несколько диаграмм Azure Data Explorer Flow](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Разъем и сот

Используйте разъем Microsoft Flow для запроса данных и их агрегирования в базе данных S'L.

> [!Note]
> Вставка S'L выполняется отдельно для каждой строки. Используйте разъем Microsoft Flow только для небольших объемов выходных данных. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> В поле *названия кластера* введите URL кластера.

## <a name="push-data-to-power-bi-dataset"></a>Нажмите данные на набор данных Power BI

Разъем Microsoft Flow можно использовать вместе с разъемом Power BI для продвижения данных из запросов Kusto в наборы потоковых данных Power BI.

1. Создайте новое действие запроса run и list results.
1. Выберите **новый шаг**.
1. Выберите **Добавить действие** и поиск Power BI.
1. Выберите **Power BI**.
1. Выберите **строки добавления в набор данных.** 

    ![Разъем Flow Power BI](./media/flow-usage/flow-powerbiconnector.png)
1. Введите **рабочее пространство,** **набор данных**и **таблицу,** в которую будут перемещены данные.
1. Из диалога динамического содержимого добавьте полезную нагрузку, содержащую схему набора данных и соответствующие результаты запроса Kusto.

    ![Поля Power BI потока](./media/flow-usage/flow-powerbifields.png)

Поток автоматически применяет действие Power BI для каждой строки таблицы результатов запроса Kusto. 

![Действие Flow Power BI для каждой строки](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Условные запросы

Результаты запросов Kusto могут быть использованы в качестве входных данных или условий для следующих действий потока.

В следующем примере мы запросим Kusto для инцидентов, которые произошли в течение последнего дня. Для каждого разрешенного инцидента публикуется неустойное сообщение и создается push-уведомление.
Для каждого инцидента, который все еще активен, Kusto запрашивается для получения дополнительной информации о подобных инцидентах. Он отправляет эту информацию в виде электронной почты и открывает соответствующую задачу TFS.

Следуйте этим инструкциям, чтобы создать аналогичный поток:

1. Создайте новое действие запроса run и list results.
1. Выберите **новый шаг**.
1. Выберите **элемент управления состоянием.**
1. Из окна динамического содержимого выберите параметр, который вы хотите использовать в качестве условия для следующих действий.
1. Выберите тип *отношения* и *значение,* чтобы установить определенное условие по данному параметру.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Поток применяет это условие в каждой строке таблицы результатов запроса.
1. Добавляйте действия, когда условие верно и ложно.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Значения результатов из запроса Kusto можно использовать в качестве ввода для следующих действий. Выберите значения результата из окна динамического содержимого.
В приведенном ниже примере были добавлены действия Slack - Post Message и Visual Studio - Создание нового элемента работы, содержащего данные из запроса Kusto.

![Slack - Действие сообщения](./media/flow-usage/flow-slack.png)

![Действие визуальной студии](./media/flow-usage/flow-visualstudio.png)

В этом примере, если инцидент все еще активен, необходимо снова задать запрос Kusto, чтобы получить информацию о том, как инциденты из того же источника были решены в прошлом.

![Запрос состояния потока](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> В поле *названия кластера* введите URL кластера.

Визуализируйте эту информацию как круговую диаграмму и отправьте ее команде по электронной почте.

![Сообщение о состоянии потока](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Отправьте по электронной почте несколько диаграмм Azure Data Explorer Flow

1. Создайте новый поток с триггером повторения и определите интервал потока и частоту. 
1. Добавьте новый шаг, с одним или более Kusto - Выполнить запрос и визуализировать результаты действий. 

    ![Выполнить несколько запросов в потоке](./media/flow-usage/flow-severalqueries.png)
1. Для каждого Kusto - Выполнить запрос и визуализировать результат, определить следующие поля:
    * URL-адрес кластера
    * Имя базы данных
    * Тип запроса и диаграммы (таблица HTML, круговая диаграмма, график времени, диаграмма бара или введите пользовательское значение).

    ![Визуализация результатов с несколькими вложениями](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Добавить отправить письмо (v2) действий: 
    1. В разделе тела выберите значок представления кода.
    1. В поле **тела** вставьте необходимый BodyHtml, чтобы визуализированный результат запроса был включен в тело электронной почты.
    1. Чтобы добавить вложение в электронную почту, добавьте имя приложения и содержимое приложения.
    
    ![Отправить несколько вложений по электронной почте](./media/flow-usage/flow-email-multiple-attachments.png)

    Полные инструкции по созданию действия по электронной почте можно узнать в [результатах запроса Email Kusto.](flow.md#email-kusto-query-results) 

Результат:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Следующие шаги

Узнайте о [разъеме Microsoft Azure Explorer Logic App,](kusto/tools/logicapps.md) который является еще одним способом автоматического выполнения запросов и команд Kusto в рамках запланированной или срабатывающей задачи.
