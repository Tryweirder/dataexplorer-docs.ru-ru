---
title: колонка .alter - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описана колонка .alter в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d41b4f452125fbfebc319112db244deaca79f37a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522616"
---
# <a name="alter-column"></a>колонка .alter

Изменяет тип данных существующего столбца таблицы.

> [!WARNING]
> При изменении типа данных столбца любые уже существующие данные в этом столбце, который не принадлежит новому типу данных, будут проигнорированы в будущих запросах и будут заменены [нулевую стоимость.](../query/scalar-data-types/null-values.md) После `alter column`использования, что данные не могут быть восстановлены, даже с помощью другой команды, чтобы изменить тип столбца обратно в предыдущее значение.
> Ниже [below](#changing-column-type-without-data-loss) приведена рекомендуемая процедура изменения типа столбца без потери данных.

**Синтаксис** 

`.alter``column` -*База данныхName* `.`- *TableName* `.` *ColumnNameColumnNewType* `type` `=` *ColumnNewType*
 
**Пример** 

```
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>Изменение типа столба без потери данных

Чтобы изменить тип столбца, сохраняя исторические данные, создайте новую правильно набранную таблицу.

Для каждой таблицы, `T1` вкакому столбцом, выполните следующие действия:

1. Создайте `T1_prime` таблицу с правильной схемой (правые типы столбцов).
1. Поменяйте таблицы с помощью команды [таблицы .rename,](rename-table-command.md) что позволяет обменивать имена таблиц.

```
.rename tables T_prime=T1, T1=T_prime
```

Когда команда завершается, новые `T1` данные перетекают в то, что `T1_prime`теперь набраны правильно и исторические данные доступны в .

До `T1_prime` тех пор, пока данные `T1` не выходят из окна `T1_prime`удержания, запросы, касающиеся, должны быть изменены для выполнения соединения с .