---
title: Управление политикой слияния - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление политикой слияния в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4093c9c09e4e268bc38cabdc6da27f0ac2ee17ab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664012"
---
# <a name="merge-policy-management"></a>Управление политикой слияния

## <a name="show-policy"></a>показать политику

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

Отображает текущую политику слияния для базы данных или таблицы.
Отображается все политики данного типа сущности (база данных или таблица), если данное имя является ''

### <a name="output"></a>Выходные данные

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|МасштабыMergeПолитика | База данных / Название таблицы | Строка jSON, представляющая политику | Список таблиц (для базы данных)|Таблица &#124; базы данных

## <a name="alter-policy"></a>изменить политику

### <a name="examples"></a>Примеры

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. Четкое установление всех свойств политики на уровне таблицы:

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000, '
    '"RowCountUpperBoundForMerge": 0, '
    '"MaxExtentsToMerge": 100, '
    '"LoopPeriod": "01:00:00", '
    '"MaxRangeInHours": 8, '
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. Четкое установление всех свойств политики на уровне базы данных:

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. Установка политики слияния *по умолчанию* на уровне базы данных:

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. Изменение одного свойства политики на уровне базы данных, сохранение всех других свойств по мере того, как это:

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. Изменение одного свойства политики на уровне таблицы, сохранение всех других свойств по мере того, как это:

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

Все вышеперечисленные возвращает обновленные степени слияния политики слияния для сущности (база данных или таблицы, указанные в качестве квалифицированного имени) в качестве их вывода.

Для вхемного изменения в политике может занять до 1 часа.

## <a name="delete-policy-of-merge"></a>удалить политику слияния

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

Команда удаляет текущую политику слияния для данного объекта.
