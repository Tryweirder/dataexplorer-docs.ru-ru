---
title: Использование Logic Apps для автоматического выполнения запросов Kusto
description: Узнайте, как использовать Logic Apps для автоматического запуска запросов и команд Kusto и планирования их выполнения.
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: ae79fd334b3ac2a59a99c5ad8d63fccf0ed6784c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875231"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Приложение логики (Майкрософт) и Azure обозреватель данных

Соединитель приложения логики Azure Kusto позволяет автоматически выполнять запросы и команды Kusto в рамках запланированной или активируемой задачи с помощью соединителя [приложения логики (Майкрософт](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) ).

Приложение логики и поток построены на одном и том же соединителе. Таким образом, [ограничения](flow.md#limitations), [действия](flow.md#azure-kusto-flow-actions), примеры [проверки подлинности](flow.md#authentication) и [использования](flow.md#azure-kusto-flow-actions) , применяемые к потоку, также применяются к Logic Apps, как упоминалось на [странице документации по Flow](flow.md).

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Как создать приложение логики с помощью Azure обозреватель данных

1. Откройте [портал Microsoft Azure](https://ms.portal.azure.com/). 
1. Найдите и выберите `logic app`.

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="Поиск "приложение логики" в портал Azure, Azure обозреватель данных" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. Щелкните **+Добавить**.

    ![Добавление приложения логики](./Images/logicapps/logicapp-add.png)

1. Введите необходимые сведения в форме:
    * Подписка
    * Группа ресурсов
    * Имя приложения логики
    * Регион или среда службы интеграции
    * Расположение
    * Включить или отключить анализ журнала
1. Выберите **Review + create** (Просмотреть и создать).

    ![Создание приложения логики](./Images/logicapps/logicapp-create-new.png)

1. При создании приложения логики выберите **изменить**.

    ![Изменить конструктор приложений логики](./Images/logicapps/logicapp-editdesigner.png "logicapp — едитдесигнер")

1. Создайте пустое приложение логики.

    ![Пустой шаблон приложения логики](./Images/logicapps/logicapp-blanktemplate.png "logicapp — бланктемплате")

1. Добавьте действие повторения и выберите **Azure Kusto**.

    ![Соединитель потока Kusto приложения логики](./Images/logicapps/logicapp-kustoconnector.png "logicapp — кустоконнектор")

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о настройке действия повторения см. на [странице документации по потокам](flow.md) .
* Ознакомьтесь с [примерами использования](flow.md#azure-kusto-flow-actions) для получения идей по настройке действий приложения логики.
