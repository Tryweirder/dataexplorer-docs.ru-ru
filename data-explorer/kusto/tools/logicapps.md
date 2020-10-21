---
title: Использование Logic Apps для автоматического выполнения запросов Kusto
description: Узнайте, как использовать Logic Apps для автоматического запуска запросов и команд Kusto и планирования их выполнения.
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 4c918c43f748f97b3bb3f6d0342c660775c1e5c8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342660"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Приложение логики (Майкрософт) и Azure обозреватель данных

Соединитель приложения логики Azure Kusto позволяет автоматически выполнять запросы и команды Kusto в рамках запланированной или активируемой задачи с помощью соединителя [приложения логики (Майкрософт](/azure/logic-apps/logic-apps-what-are-logic-apps) ).

Приложение логики и автоматизация энергосбережения основаны на одном соединителе. Таким образом, [ограничения](../../flow.md#limitations), [действия](../../flow.md#flow-actions), примеры [проверки подлинности](../../flow.md#authentication) и [использования](../../flow-usage.md) , применяемые к автоматизации питания, также применяются к Logic Apps, как упоминалось на [странице документации по Power автоматизиру](../../flow.md).

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Как создать приложение логики с помощью Azure обозреватель данных

1. Откройте [портал Microsoft Azure](https://ms.portal.azure.com/). 
1. Найдите и выберите `logic app`.

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="Поиск &quot;приложение логики&quot; в портал Azure, Azure обозреватель данных" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. Щелкните **+Добавить**.

    ![Добавление приложения логики](./Images/logicapps/logicapp-add.png)

1. Введите необходимые сведения в форме:
    * Подписка
    * Группа ресурсов
    * Имя приложения логики
    * Регион или среда службы интеграции
    * Расположение
    * Включить или отключить анализ журнала
1. Выберите **Проверить и создать**.

    ![Создание приложения логики](./Images/logicapps/logicapp-create-new.png)

1. При создании приложения логики выберите **изменить**.

    ![Изменить конструктор приложений логики](./Images/logicapps/logicapp-editdesigner.png "logicapp — едитдесигнер")

1. Создайте пустое приложение логики.

    ![Пустой шаблон приложения логики](./Images/logicapps/logicapp-blanktemplate.png "logicapp — бланктемплате")

1. Добавьте действие повторения и выберите **Azure Kusto**.

    ![Соединитель потока Kusto приложения логики](./Images/logicapps/logicapp-kustoconnector.png "logicapp — кустоконнектор")

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о настройке действия повторения см. на [странице документации по Power автоматизиру](../../flow.md) .
* Ознакомьтесь с [примерами использования](../../flow-usage.md) для получения идей по настройке действий приложения логики.
