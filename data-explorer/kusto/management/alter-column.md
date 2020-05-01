---
title: . ALTER COLUMN — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается. ALTER COLUMN в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: ecf0fa09438f8df5792d8826150d58f06360cace
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617873"
---
# <a name="alter-column"></a>.alter column

Изменяет тип данных существующего столбца таблицы.

> [!WARNING]
> При изменении типа данных столбца все уже существующие данные в этом столбце, которые не принадлежат новому типу данных, будут игнорироваться в будущих запросах и будут заменены [значением NULL](../query/scalar-data-types/null-values.md). После использования `alter column`эти данные нельзя восстановить даже с помощью другой команды, чтобы изменить тип столбца обратно на предыдущее значение.
> [Ниже](#changing-column-type-without-data-loss) приведена рекомендуемая процедура изменения типа столбца без потери данных.

**Синтаксис** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *ColumnName* ColumnName `type` *ColumnNewType* колумнневтипе `=`
 
**Пример** 

```kusto
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>Изменение типа столбца без потери данных

Чтобы изменить тип столбца с сохранением исторических данных, создайте новую, правильно типизированную таблицу.

Для каждой таблицы `T1` , в которую вы хотите изменить тип столбца, выполните следующие действия.

1. Создайте таблицу `T1_prime` с правильной схемой (типами столбцов справа).
1. Переключить таблицы с помощью команды [. Rename Tables](rename-table-command.md) , которая позволяет менять имена таблиц.

```kusto
.rename tables T_prime=T1, T1=T_prime
```

После выполнения команды новые потоки данных будут `T1` правильно введены, а исторические данные будут доступны в. `T1_prime`

Пока `T1_prime` данные не выходят из окна сохранения, необходимо изменить запросы `T1` , чтобы выполнить объединение с `T1_prime`.