---
title: таблица .rename и таблицы .rename - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны таблицы .rename и .rename таблицы в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: db3e38c562573f52df70979b71fe72d8947158bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520491"
---
# <a name="rename-table-and-rename-tables"></a>таблица .rename и таблицы .rename

Изменяет название существующей таблицы.

Команда `.rename tables` изменяет название нескольких таблиц в базе данных как единую транзакцию.

Требуется [разрешение админа для получения данных.](../management/access-control/role-based-authorization.md)

**Синтаксис**

`.rename``table` *OldName* `to` *NewName*

`.rename``tables` *NewName* = *OldName* `ifexists``,`

> [!NOTE]
> * *OldName* — это название существующей таблицы. Ошибка поднята и вся команда завершается сбоем (не имеет эффекта), если *OldName* не называет существующую таблицу, если не `ifexists` указана (в этом случае эта часть команды переименования игнорируется).
> * *NewName* — это новое название существующей таблицы, которая раньше называлась *OldName.*
> * Если `ifexists` указано, это изменяет поведение команды, чтобы игнорировать переименование частей несуществующих таблиц.

**Замечания**

Эта команда работает только на таблицах базы данных.
Имена таблиц не могут быть квалифицированы с именами кластеров или баз данных.

Эта команда не создает новых таблиц и не удаляет существующие таблицы.
Преобразование, описанное командой, должно быть таким, чтобы количество таблиц в базе данных не меняться.

Команда **поддерживает** обмен именами таблиц или более сложные перестановки, если они придерживаются вышеуказанных правил. Например, глотать данные в несколько промежуточных таблиц, а затем поменять их с существующими таблицами в одной транзакции.

**Примеры**

Представьте себе базу данных `A` `B`со `C`следующими таблицами: , , и `A_TEMP`.
Следующая команда `A` будет `A_TEMP` меняться `A_TEMP` и (так `A`что таблица теперь будет `B` `NEWB`называться `C` , и наоборот), переименовать в , и держать как есть. 

```
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

Следующая последовательность команд:
1. Создает новую временную таблицу
1. Заменяет существующую или несуществующую таблицу новой таблицей

```
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```