---
title: Политика ограниченного доступа в режим доступа к Интернету - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается политика RestrictedView Access в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6f994f5b80632650ab6dbe5dcf28cd82407d688f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520423"
---
# <a name="restrictedviewaccess-policy"></a>Политика ограниченногодоступа

*RestrictedViewAccess* — это дополнительная политика, которая может быть включена на столах в базе данных.

Когда эта политика включена в таблице, данные в таблице могут быть запрошены *только* к принципам, добавленным к роли [UnrestrictedViewer](../management/access-control/role-based-authorization.md) в базе данных.

Когда политика включена в таблице, любой директор (даже таблица/база данных/кластерный админ), который не включен в роль уровня базы данных [UnrestrictedViewer,](../management/access-control/role-based-authorization.md) не сможет заставить данные в таблице.

Роль [UnrestrictedViewer](../management/access-control/role-based-authorization.md) предоставляет разрешение на представление *всем* таблицам в базе данных, в которых включена политика, предполагая, что текущий принцип уже уполномочен задавать запрос на базу данных (админ/пользователь/зритель базы данных). Добавление или удаление принципов в или из роли может быть сделано [DatabaseAdmin](../management/access-control/role-based-authorization.md).

> [!NOTE]
> Политика RestrictedViewAccess не может быть настроена на стол, на котором включена [политика безопасности уровня строки.](./rowlevelsecuritypolicy.md)

Подробнее о командах управления для управления политикой RestrictedViewAccess [смотрите здесь.](../management/restrictedviewaccess-policy.md)