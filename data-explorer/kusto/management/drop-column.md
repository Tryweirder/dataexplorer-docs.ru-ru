---
title: Drop Column — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается удаление столбца в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 770f787493828e897600485c282f3ccb12bb74c4
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966879"
---
# <a name="drop-column"></a>.drop column

Удаляет столбец из таблицы.
Сведения о том, как удалить несколько столбцов из таблицы, см. [ниже](#drop-table-columns).

> [!WARNING]
> Эта команда необратима. Все данные в удаляемом столбце будут удалены.
> Будущие команды для добавления этого столбца назад не смогут восстановить данные.

**Синтаксис**

`.drop``column` *TableName* `.` *ColumnName*

## <a name="drop-table-columns"></a>удалить столбцы таблицы

Удаляет несколько столбцов из таблицы.

> [!WARNING]
> Эта команда необратима. Все данные в удаляемых столбцах будут удалены.
> Дальнейшие команды по добавлению этих столбцов назад не смогут восстановить данные.

**Синтаксис**

`.drop``table` *TableName* `columns` TableName `(` *Col1* [ `,` *Col2*]...`)`