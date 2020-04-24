---
title: .alter база данных довольное имя - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье в Azure Data Explorer описывается база данных .alter.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b362b547dc980676108ec169a0abb97f189375b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522599"
---
# <a name="alter-database-prettyname"></a>.alter база данных довольное имя

Изменяет красивое (дружественное) имя базы данных.

Требуется [разрешение DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.alter``database` База `prettyname` `'` *данныхНаистомы* *DatabaseName*`'`

**Выход возврата**
 
|Выходной параметр |Тип |Описание 
|---|---|---
|имя_базы_данных |Строка |Имя базы данных.
|PrettyName |Строка |Красивое название базы данных.

