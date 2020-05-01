---
title: . переименуйте таблицу и переименуйте таблицы в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Переименование таблицы и переименование таблиц в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a1644021d1e2df294782d3b24e111d3c57af5f91
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617584"
---
# <a name="rename-table-and-rename-tables"></a>. переименуйте таблицу и переименуйте таблицы.

Изменяет имя существующей таблицы.

`.rename tables` Команда изменяет имя ряда таблиц в базе данных как единую транзакцию.

Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.rename``table` *OldName* Oldname `to` *newname*

`.rename``tables` *NewName*Newname = *oldname* [`ifexists`] [`,` ...]

> [!NOTE]
> * *Oldname* — имя существующей таблицы. Возникает ошибка, и вся команда завершается ошибкой (не действует), если *oldname* не имеет имени существующей таблицы, если не `ifexists` указан параметр (в этом случае эта часть команды rename не учитывается).
> * *Newname* — новое имя существующей таблицы, которая использовалась для вызова *oldname*.
> * Если `ifexists` указан параметр, он изменяет поведение команды таким образом, чтобы не пропускать переименование частей несуществующих таблиц.

**Замечания**

Эта команда работает с таблицами базы данных только в области.
Имена таблиц не могут уточняться именем кластера или базы данных.

Эта команда не создает новые таблицы и не удаляет существующие таблицы.
Преобразование, описываемое командой, должно быть таким, чтобы число таблиц в базе данных не изменилось.

**Команда поддерживает** замену имен таблиц или более сложных перестановок при условии, что они соответствуют приведенным выше правилам. Например, прием данных в несколько промежуточных таблиц с последующим их переключением на существующие таблицы в одной транзакции.

**Примеры**

Представьте себе базу данных со следующими таблицами `A`: `B`, `C`, и `A_TEMP`.
Следующая команда `A` перемещает и `A_TEMP` (так что `A_TEMP` таблица будет вызываться `A`и другим способом), переименуйте `B` в `NEWB`и продолжите `C` как есть. 

```kusto
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

Следующая последовательность команд:
1. Создает новую временную таблицу
1. Заменяет существующую или несуществующую таблицу новой таблицей

```kusto
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```
