---
title: Переименование столбца в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается переименование столбцов в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25d0ff106d406c59c24d26542a8dad1e4992e311
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967389"
---
# <a name="rename-column"></a>. Переименование столбца

Изменяет имя существующего столбца таблицы.
Чтобы изменить имя нескольких столбцов, см. раздел [ниже](#rename-columns).

**Синтаксис**

`.rename``column`[*DatabaseName* `.` ] *имя_таблицы* `.` *колумнексистингнаме* `to` *колумнневнаме*

Где *DatabaseName*, *TableName*, *колумнексистингнаме*и *колумнневнаме* — имена соответствующих сущностей и следуют [правилам именования идентификаторов](../query/schema-entities/entity-names.md).

## <a name="rename-columns"></a>переименование столбцов

Изменяет имена нескольких существующих столбцов в одной таблице.

**Синтаксис**

`.rename``columns` *Col1* `=` [*DatabaseName* `.` [*TableName* `.` *Col2*]] `,` ...

Команда может использоваться для замены имен двух столбцов (каждый из них переименовывается именем другого).