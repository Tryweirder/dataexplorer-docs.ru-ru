---
title: Различия между сервером «Кусто» (Kusto Microsoft S'L Server) — Azure Data Explorer Документы Майкрософт
description: В этой статье описаны различия между сервером Ms-TDS/T-S'L между сервером Kusto Microsoft S'L в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: be294053fdd0f95d488f52b547ef7abd0ef7c23c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523432"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>Разницы между сервером «Кусто» microsoft S'L

Ниже приведен частичный список основных отличий между реализацией T-S'SL-сервера Kusto и S'L Server.

## <a name="create-insert-drop-alter-statements"></a>CREATE, INSERT, DROP, ALTER заявления

Kusto не поддерживает изменения схем или изменения данных через MS-TDS, а также не поддерживает вышеуказанные заявления T-S'L.

## <a name="correlated-sub-queries"></a>Коррелированные подзапросы

Kusto не поддерживает коррелированные подзапросы в `SELECT`, `WHERE`и `JOIN` положения.

## <a name="top-flavors"></a>ТОП-ароматизаторы

Кусто игнорирует `WITH TIES` и оценивает запрос как `TOP`регулярный.
Кусто не поддерживает. `PERCENT`

## <a name="cursors"></a>Курсоры

Kusto не поддерживает курсоры S'L.

## <a name="flow-control"></a>Управление потоком

Kusto не поддерживает операторы управления потоками, за исключением нескольких ограниченных случаев, таких как `IF` `THEN` `ELSE` положение, которое имеет идентичную схему `THEN` для и `ELSE` пакетов.

## <a name="data-types"></a>Типы данных

В зависимости от запроса возвращенные данные могут иметь другой тип, чем в сервере S'L.
Очевидным примером здесь являются `TINYINT` `SMALLINT` такие типы, как и которые не имеют эквивалента в Кусто. Таким образом, клиенты, `BYTE` которые `INT16` ожидают `INT32` значение `INT64` типа или могут получить или значение вместо.

## <a name="column-order-in-results"></a>Заказ столбца в результатах

Когда в `SELECT` отчете используется астерикс, порядок столбцов в каждом наборе результатов может отличаться между Kusto и S'L Server. Клиент, который использует имена столбцов, будет работать лучше в этих случаях.
Если в `SELECT` заявлении нет символа астерикса, то ординаторы столбцов будут сохранены.

## <a name="columns-name-in-results"></a>Название столбцов в результатах

В T-S'L несколько столбцов могут иметь одно и то же имя. Это запрещено в Кусто.
В случае столкновения в именах названия столбцов могут отличаться в Кусто.
Тем не менее, первоначальное название будет сохранено, по крайней мере для одной из столбцов.

## <a name="any-all-and-exists-predicates"></a>НИКОГДА, ВСЕ, и EXISTS предикаты

Кусто не поддерживает предикаты, `ANY` `ALL`и `EXISTS`.

## <a name="recursive-ctes"></a>Рекурсивные CTE

Kusto не поддерживает рекурсивные общие выражения таблицы.

## <a name="dynamic-sql"></a>Динамический SQL

Kusto не поддерживает динамические операторы S'L (встроенное выполнение скрипта S'L, генерируемого запросом).

## <a name="within-group"></a>WITHIN GROUP

Кусто не поддерживает `WITHIN GROUP` оговорку.

## <a name="truncate-function"></a>Функция TRUNCATE

Функция TRUNCATE (ODBC) в Кусто работает аналогично ROUND, что означает, что результат будет ближайшим значением, а не нижним, возвращенным в S'L.