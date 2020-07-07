---
title: Управление политиками слияния — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается управление политиками слияния в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4fd1e9e1c67e03a09bf817dc977ee833d3de789c
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967423"
---
# <a name="merge-policy-command"></a>Команда политики слияния

## <a name="show-policy"></a>отображение политики

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

Отображает текущую политику слияния для базы данных или таблицы.
Показывает все политики заданного типа сущности (база данных или таблица), если заданное имя "*".

### <a name="output"></a>Выходные данные

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|екстентсмержеполици | Имя базы данных или таблицы | строка в формате JSON, представляющая политику. | Список таблиц (для базы данных)|Таблица &#124; базы данных

## <a name="alter-policy"></a>изменение политики

### <a name="examples"></a>Примеры

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. явное задание всех свойств политики на уровне таблицы:

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. явное задание всех свойств политики на уровне базы данных:

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. Настройка политики слияния *по умолчанию* на уровне базы данных:

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. изменение одного свойства политики на уровне базы данных, сохраняя все остальные свойства как есть:

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. изменение одного свойства политики на уровне таблицы, сохраняя все остальные свойства как есть:

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

Все вышеперечисленное возвращает обновленную политику слияния экстентов для сущности (базы данных или таблицы, указанной в качестве полного имени) в качестве выходных данных.

Для вступления в силу изменений политики может потребоваться до 1 часа.

## <a name="delete-policy-of-merge"></a>Удаление политики слияния

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

Команда удаляет текущую политику слияния для данной сущности.
