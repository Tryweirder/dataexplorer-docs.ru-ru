---
title: Kusto Рестриктедвиевакцесс политика управляет запросами в Azure обозреватель данных
description: В этой статье описывается политика Рестриктедвиевакцесс в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e44aa2b14aa8babdab95a6ad8c6f7ef5ed026ff9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617432"
---
# <a name="restrictedviewaccess-policy"></a>Политика Рестриктедвиевакцесс

*Рестриктедвиевакцесс* — это необязательная политика, которая может быть включена для таблиц в базе данных.

Если эта политика включена для таблицы, данные в таблице могут быть запрошены *только* для участников, добавленных в роль [унрестриктедвиевер](../management/access-control/role-based-authorization.md) в базе данных.

Если политика включена для таблицы, любой участник (даже таблица или база данных или администратор кластера), который не включен в роль уровня базы данных [унрестриктедвиевер](../management/access-control/role-based-authorization.md) , не сможет запрашивать данные в таблице.

Роль [унрестриктедвиевер](../management/access-control/role-based-authorization.md) предоставляет разрешение VIEW для *всех* таблиц в базе данных, для которых включена политика, предполагая, что текущий участник уже имеет право на запрос к базе данных (администратор базы данных, пользователь или средство просмотра). Добавление или удаление участников роли можно выполнять с помощью [датабасеадмин](../management/access-control/role-based-authorization.md).

> [!NOTE]
> Невозможно настроить политику Рестриктедвиевакцесс для таблицы, в которой включена [политика безопасность на уровне строк](./rowlevelsecuritypolicy.md) .

Дополнительные сведения об управляющих командах для управления политикой Рестриктедвиевакцесс [см. здесь](../management/restrictedviewaccess-policy.md).