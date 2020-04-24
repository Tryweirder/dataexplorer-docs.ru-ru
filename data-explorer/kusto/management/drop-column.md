---
title: колонка падения - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается столбец падения в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 56ba5499b27b517b3080ee27ac317aa1e0917edd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521222"
---
# <a name="drop-column"></a>падение столбец

Удаляет столбец со стола.
Чтобы выбросить несколько столбцов из таблицы, [см.](#drop-table-columns)

> [!WARNING]
> Эта команда необратима. Все данные в удаленной колонке будут удалены.
> Будущие команды, чтобы добавить, что столбец назад не сможет восстановить данные.

**Синтаксис**

`.drop``column` *ТаблицаИмя* `.` *ColumnName*

## <a name="drop-table-columns"></a>падение столбцов таблицы

Удаляет несколько столбцов со стола.

> [!WARNING]
> Эта команда необратима. Все данные в столбцах, которые удаляются, будут удалены.
> Будущие команды для добавления этих столбцов не смогут восстановить данные.

**Синтаксис**

`.drop``table` *ТаблицаИмя* `columns` `(` *Col1* `,` *Col2*`)`