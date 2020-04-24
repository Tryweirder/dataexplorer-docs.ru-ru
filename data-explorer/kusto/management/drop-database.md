---
title: .drop база данных довольное имя - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье в Azure Data Explorer описывается база данных .drop.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8a74b871ddcf420f39bb45ebdf3bce36f026105
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521188"
---
# <a name="drop-database-prettyname"></a>.drop база данных довольное имя

Отбрасывает красивое (дружественное) имя базы данных.
Требуется [разрешение DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.drop``database` *База данныхИмя*`prettyname`

**Выход возврата**
 
|Выходной параметр |Тип |Описание 
|---|---|---
|имя_базы_данных |Строка |Имя базы данных
|PrettyName |Строка |База данных красивое имя (null после операции падения)