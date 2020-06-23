---
title: Скалярные типы данных в Azure Data Explorer | Документация Майкрософт
description: В этой статье описываются скалярные типы данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 95bb28c81ec3221569758ead8a289bdf81d32d3d
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128654"
---
# <a name="scalar-data-types"></a>Скалярные типы данных

Каждое значение данных (например, значение выражения, параметр функции или значение выражения) имеет определенный **тип данных**. Типы данных можно разделить на **скалярные типы данных** (встроенные предопределенные типы, которые перечислены ниже) или **определяемые пользователем записи** (упорядоченная последовательность пар "имя — скалярный тип данных", например тип данных для строки таблицы).

Kusto предоставляет набор системных типов данных, которые определяют все возможные типы данных для использования в Kusto.

> [!NOTE]
> Пользовательские типы данных в Kusto не поддерживаются.

В следующей таблице перечислены все поддерживаемые в Kusto типы данных, а также дополнительные псевдонимы для ссылки на них и приблизительные эквиваленты типов для .NET Framework.

| Тип       | Дополнительные имена   | Эквивалентный тип .NET              | gettype()   |
| ---------- | -------------------- | --------------------------------- | ----------- |
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |
| `dynamic`  |                      | `System.Object`                   | `array`, `dictionary`, или любое другое значение |
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |
| `int`      |                      | `System.Int32`                    | `int`       |
| `long`     |                      | `System.Int64`                    | `long`      |
| `real`     | `double`             | `System.Double`                   | `real`      |
| `string`   |                      | `System.String`                   | `string`    |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   |

Все типы данных, кроме строковых, принимают специальное значение NULL, которое обозначает отсутствие данных или несоответствие данных. Например, попытка приема строки `"abc"` в столбец `int` передает такое значение.
Это значение невозможно вычислить явным образом, но вы можете определить такой результат выражения с помощью функции `isnull()`.

> [!WARNING]
> Тип `guid` поддерживается частично.
> Мы настоятельно рекомендуем разработчикам использовать вместо него значения с типом `string`.
