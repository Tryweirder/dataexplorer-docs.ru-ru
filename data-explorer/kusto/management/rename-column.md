---
title: переименование столбца - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается колонка переименований в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 02e692e01bb8eb0abd49c9673b000b722734db90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520508"
---
# <a name="rename-column"></a>переименование столбца

Изменяет название существующего столбца таблицы.
Чтобы изменить название нескольких столбцов, [см.](#rename-columns)

**Синтаксис**

`.rename``column` -*База данныхName* `.`- *TableName* `.` *ColumnExistingName* `to` *ColumnNewName*

Где *DatabaseName,* *TableName,* *ColumnExistingName*и *ColumnNewName* являются именами соответствующих сущностей и следуют [правилам именования идентификаторов.](../query/schema-entities/entity-names.md)

## <a name="rename-columns"></a>переименование столбцов

Изменяем имена нескольких существующих столбцов в одной таблице.

**Синтаксис**

`.rename``columns` *Col1* `=` -*База данныхName* `.` - `,` *TableName* `.` *Col2*...

Команда может быть использована для обмена именами двух столбцов (каждый переименован в имя другого).