---
title: Скалярные типы данных в Azure Data Explorer | Документация Майкрософт
description: В этой статье описываются скалярные типы данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 3ef87217beee62fe4cecf7ee95dfe8daba49af7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490248"
---
# <a name="scalar-data-types"></a>Скалярные типы данных

Каждое значение данных (например, значение выражения, параметр функции или значение выражения) имеет определенный **тип данных**. Типы данных можно разделить на **скалярные типы данных** (встроенные предопределенные типы, которые перечислены ниже) или **определяемые пользователем записи** (упорядоченная последовательность пар "имя — скалярный тип данных", например тип данных для строки таблицы).

Kusto предоставляет набор системных типов данных, которые определяют все возможные типы данных для использования в Kusto.

> [!NOTE]
> Пользовательские типы данных в Kusto не поддерживаются.

В следующей таблице перечислены все поддерживаемые в Kusto типы данных, а также дополнительные псевдонимы для ссылки на них и приблизительные эквиваленты типов для .NET Framework.

| Тип       | Дополнительные имена   | Эквивалентный тип .NET              | gettype()   |Тип хранилища (внутреннее имя)|
| ---------- | -------------------- | --------------------------------- | ----------- |----------------------------|
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |`I8`                        |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |`DateTime`                  |
| `dynamic`  |                      | `System.Object`                   | `array`, `dictionary`, или любое другое значение |`Dynamic`|
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |`UniqueId`                  |
| `int`      |                      | `System.Int32`                    | `int`       |`I32`                       |
| `long`     |                      | `System.Int64`                    | `long`      |`I64`                       |
| `real`     | `double`             | `System.Double`                   | `real`      |`R64`                       |
| `string`   |                      | `System.String`                   | `string`    |`StringBuffer`              |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |`TimeSpan`                  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   | `Decimal`                  |

Все типы данных принимают специальное значение NULL, которое обозначает отсутствие данных или несоответствие данных. Например, попытка приема строки `"abc"` в столбец `int` передает такое значение.
Это значение невозможно вычислить явным образом, но вы можете определить такой результат выражения с помощью функции `isnull()`.

> [!WARNING]
> На момент написания этой статьи поддержка типа `guid` является неполной. Мы настоятельно рекомендуем разработчикам использовать вместо него значения с типом `string`.

