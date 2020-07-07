---
title: Управление политиками секционирования данных в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается управление политиками секционирования данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 4f5abfd5c7fffd126033baeb2bbb9243b4400f58
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967406"
---
# <a name="partitioning-policy-command"></a>команда политики секционирования

Политика секционирования данных подробно описана [здесь](../management/partitioningpolicy.md).

## <a name="show-policy"></a>отображение политики

```kusto
.show table [table_name] policy partitioning
```

`.show`Команда отображает политику секционирования, применяемую к таблице.

### <a name="output"></a>Выходные данные

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|Секционирование | Имя таблицы | Сериализация JSON объекта политики | null | Таблица

## <a name="alter-and-alter-merge-policy"></a>изменение и изменение политики слияния

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

`.alter`Команда позволяет изменить политику секционирования, применяемую к таблице.

Для команды требуются разрешения [датабасеадмин](access-control/role-based-authorization.md) .

Для вступления в силу изменений политики может потребоваться до 1 часа.

### <a name="examples"></a>Примеры

#### <a name="setting-a-policy-with-a-hash-partition-key"></a>Настройка политики с ключом хэш-секции

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
    '}'
  ']'
'}'
```

#### <a name="setting-a-policy-with-a-uniform-range-datetime-partition-key"></a>Задание политики с равномерным ключом секции даты и времени

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
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

#### <a name="setting-a-policy-with-both-kinds-of-partition-keys"></a>Настройка политики для обоих типов ключей секций

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

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>Явное задание определенного свойства политики на уровне таблицы

Чтобы задать `EffectiveDateTime` для политики другое значение, используйте следующую команду:

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>удалить политику

```kusto
.delete table [table_name] policy partitioning
```

`.delete`Команда удаляет политику секционирования для заданной таблицы.
