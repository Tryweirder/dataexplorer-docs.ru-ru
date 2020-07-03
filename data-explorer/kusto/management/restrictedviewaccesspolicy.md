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
ms.openlocfilehash: 9ba9f8b0f0a940357eab2277eb24e18b92718bc4
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85901800"
---
# <a name="restrictedviewaccess-policy"></a>Политика Рестриктедвиевакцесс

*Рестриктедвиевакцесс* — это необязательная политика, которая может быть включена для таблиц базы данных.

Если эта политика включена для таблицы, данные в таблице могут запрашиваться только участниками, у которых есть роль [унрестриктедвиевер](../management/access-control/role-based-authorization.md) в базе данных.
Любой участник, не зарегистрированный в роли уровня базы данных [унрестриктедвиевер](../management/access-control/role-based-authorization.md) , не сможет запрашивать данные в таблице. Даже Незарегистрированная таблица, база данных или администратор кластера.

Роль [унрестриктедвиевер](../management/access-control/role-based-authorization.md) предоставляет разрешение VIEW для *всех* таблиц в базе данных, для которых включена политика.
Текущий участник, администратор базы данных, пользователь или средство просмотра, уже имеют право на выполнение запросов к базе данных. Добавление или удаление участников может осуществляться с помощью [датабасеадмин](../management/access-control/role-based-authorization.md).

> [!NOTE]
> Невозможно настроить политику Рестриктедвиевакцесс для таблицы, в которой включена [политика безопасность на уровне строк](./rowlevelsecuritypolicy.md) .

Дополнительные сведения см. в разделе Управляющие команды для управления [политикой рестриктедвиевакцесс](../management/restrictedviewaccess-policy.md).
