---
title: Используйте Logic Apps, чтобы автоматически выполнять запросы Kusto в Azure обозреватель данных
description: Узнайте, как использовать Logic Apps для автоматического запуска запросов и команд Kusto и планирования их выполнения.
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: a4f79fc367e2769dfe3bf51ed5ad035a7d233ea4
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902677"
---
# <a name="microsoft-logic-app-and-azure-data-explorer-preview"></a>Приложение логики (Майкрософт) и обозреватель данных Azure (Предварительная версия)

Соединитель приложения логики Azure Kusto позволяет автоматически выполнять запросы и команды Kusto в рамках запланированной или активируемой задачи с помощью соединителя [приложения логики (Майкрософт](/azure/logic-apps/logic-apps-what-are-logic-apps) ).

Приложение логики и автоматизация энергосбережения основаны на одном соединителе. Таким образом, [ограничения](../../flow.md#limitations), [действия](../../flow.md#flow-actions), примеры [проверки подлинности](../../flow.md#authentication) и [использования](../../flow-usage.md) , применяемые к автоматизации питания, также применяются к Logic Apps, как упоминалось на [странице документации по Power автоматизиру](../../flow.md).

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Как создать приложение логики с помощью Azure обозреватель данных

1. Откройте [портал Microsoft Azure](https://ms.portal.azure.com/). 
1. Найдите и выберите `logic app`.

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="Поиск &quot;приложение логики&quot; в портал Azure, Azure обозреватель данных" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. Щелкните **+Добавить** .

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

1. При создании приложения логики выберите **изменить** .

    ![Изменить конструктор приложений логики](./Images/logicapps/logicapp-editdesigner.png "logicapp — едитдесигнер")

1. Создайте пустое приложение логики.

    ![Пустой шаблон приложения логики](./Images/logicapps/logicapp-blanktemplate.png "logicapp — бланктемплате")

1. Добавьте действие повторения и выберите **Azure Kusto** .

    ![Соединитель потока Kusto приложения логики](./Images/logicapps/logicapp-kustoconnector.png "logicapp — кустоконнектор")

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о настройке действия повторения см. на [странице документации по Power автоматизиру](../../flow.md) .
* Ознакомьтесь с [примерами использования](../../flow-usage.md) для получения идей по настройке действий приложения логики.
