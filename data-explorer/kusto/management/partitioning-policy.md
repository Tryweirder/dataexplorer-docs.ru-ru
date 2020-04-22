---
title: Управление политикой разгородки данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление политикой раздела данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 0e1ff783195f26adf7f98e511ca155f43609098c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663966"
---
# <a name="data-partitioning-policy-management"></a>Управление политикой раздела данных

Политика раздела данных подробно описана [здесь.](../management/partitioningpolicy.md)

## <a name="show-policy"></a>показать политику

```kusto
.show table [table_name] policy partitioning
```

Команда `.show` отображает политику раздела, которая применяется на столе.

### <a name="output"></a>Выходные данные

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|Передача данных | Имя таблицы | Серияизация объекта политики JSON | null | Таблица

## <a name="alter-and-alter-merge-policy"></a>изменить и изменить-слияние политики

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

Команда `.alter` позволяет изменить политику раздела, которая применяется на столе.

Команда требует разрешения [DatabaseAdmin.](access-control/role-based-authorization.md)

Для вхемного изменения в политике может занять до 1 часа.

### <a name="examples"></a>Примеры

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>Установка всех свойств политики явно на уровне таблицы

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '},'
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>Установка конкретного свойства политики явно на уровне таблицы

Чтобы установить `EffectiveDateTime` политику на другое значение, используйте следующую команду:

```kusto
.alter table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>политика удаления

```kusto
.delete table [table_name] policy partitioning
```

Команда `.delete` удаляет политику раздела данной таблицы.
