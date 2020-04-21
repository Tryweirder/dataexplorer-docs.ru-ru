---
title: Microsoft Logic App и Kusto - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описаны microsoft Logic App и Kusto в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f7d719ece5df6eb3f6d4060a2fb07e7092902601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523823"
---
# <a name="microsoft-logic-app-and-kusto"></a>Microsoft Logic App и Kusto

Разъем Azure Kusto Logic App позволяет пользователям автоматически запускать запросы и команды Kusto в рамках запланированной или срабатывающей задачи, используя [Microsoft Logic App.](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)

Разъемы Logic App и Flow построены поверх одного и того же разъема, поэтому те же [ограничения,](flow.md#limitations) [действия,](flow.md#azure-kusto-flow-actions) [аутентификация](flow.md#authentication) и [примеры использования](flow.md#usage-examples) применяются как для упомянутых на [странице документации потока.](flow.md)


## <a name="how-to-create-a-logic-app-with-azure-kusto"></a>Как создать logic App с Azure Kusto

Откройте портал Azure и нажмите кнопку создания нового ресурса Logic App.
Добавьте нужное имя, подписку, группу и месторасположение и нажмите создать.

![Создание приложения логики](./Images/KustoTools-LogicApp/logicapp-createlogicapp.png "logicapp-createlogicapp")

После создания logic App нажмите кнопку «Отодвинет кнопку»

![Отодвило ели разработчик приложения для логики](./Images/KustoTools-LogicApp/logicapp-editdesigner.png "logicapp-editdesigner")

Создание пустого приложения логики

![Чистый шаблон приложения логики](./Images/KustoTools-LogicApp/logicapp-blanktemplate.png "logicapp-blanktemplate")

Добавить действие повторения, а затем выбрать 'Azure Kusto'

![Логика приложение Кусто поток разъем](./Images/KustoTools-LogicApp/logicapp-kustoconnector.png "logicapp-kustoconnector")