---
title: Управление политикой осколков - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление политикой Sharding в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7770e0834e4b00f42158732e667d41eb636cec5b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520049"
---
# <a name="sharding-policy-management"></a>Управление политикой осколков

## <a name="show-policy"></a>показать политику

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`политика отображает политику осколков для базы данных или таблицы. Он показывает все политики данного типа сущности (база данных или таблицы), если данное имя 'к' .

### <a name="output"></a>Выходные данные

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|ExtentsHardingПолитика | база данных / название таблицы | строка формата json, представляющая политику | список таблиц (для базы данных)|база данных / таблица

## <a name="alter-policy"></a>изменить политику

### <a name="examples"></a>Примеры

Следующие примеры возвращают обновленную политику осколков уровней для сущности, в качестве вывода — база данных или таблица, указанная в качестве квалифицированного имени.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Установка всех свойств политики явно на уровне таблицы

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>Установка всех свойств политики явно на уровне базы данных

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>Настройка политики застывания *по умолчанию* на уровне базы данных

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>Изменение одного свойства политики на уровне базы данных 

Храните все другие свойства как есть.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>Изменение одного свойства политики на уровне таблицы

Храните все другие свойства как

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>политика удаления

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

Команда удаляет текущую политику осколков для данного объекта.