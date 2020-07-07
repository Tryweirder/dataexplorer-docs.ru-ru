---
title: Управление политиками сегментирования в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается управление политиками сегментирования в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb4ff4ade5e3fb0e2f01de0adc74aecd27381a3d
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967474"
---
# <a name="sharding-policy-command"></a>команда политики сегментирования

## <a name="show-policy"></a>отображение политики

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`Политика отображает политику сегментирования для базы данных или таблицы. В нем отображаются все политики заданного типа сущности (база данных или таблица), если заданное имя имеет значение "*".

### <a name="output"></a>Выходные данные

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|екстентсшардингполици | имя базы данных или таблицы | Строка формата JSON, представляющая политику | список таблиц (для базы данных)|база данных/таблица

## <a name="alter-policy"></a>изменение политики

### <a name="examples"></a>Примеры

В следующих примерах возвращается Обновленная политика сегментирования экстентов для сущности с базой данных или таблицей, указанной как полное имя, в качестве выходных данных.

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Явное задание всех свойств политики на уровне таблицы

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>Явное задание всех свойств политики на уровне базы данных

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>Настройка политики сегментирования *по умолчанию* на уровне базы данных

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>Изменение одного свойства политики на уровне базы данных 

Сохраняет все остальные свойства как есть.

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>Изменение одного свойства политики на уровне таблицы

Все остальные свойства должны быть "как есть"

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>удалить политику

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

Команда удаляет текущую политику сегментирования для заданной сущности.