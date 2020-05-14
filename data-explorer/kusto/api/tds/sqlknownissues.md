---
title: Различия между Kusto MS-TDS/T-SQL и SQL Server-Azure обозреватель данных
description: В этой статье описываются различия между Kusto Microsoft SQL Server в Azure обозреватель данных с MS-TDS/T-SQL.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: c949b5bb3659d82ed586a39b4310495e61934777
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382375"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>Отличия MS-TDS/T-SQL от Kusto Microsoft SQL Server

Ниже приведен частичный список основных различий между Kusto SQL Server и реализацией языка T-SQL.

## <a name="create-insert-drop-alter-statements"></a>Создание, вставка, удаление, инструкции ALTER

Kusto не поддерживает модификации схемы или изменение данных с помощью MS-TDS и не поддерживает приведенные выше инструкции T-SQL.

## <a name="correlated-sub-queries"></a>Коррелированные вложенные запросы

Kusto не поддерживает коррелированные вложенные запросы в `SELECT` `WHERE` `JOIN` предложениях, и.

## <a name="top-flavors"></a>Основные разновидности

Kusto игнорирует `WITH TIES` и оценивает запрос как обычный `TOP` .
Kusto не поддерживает `PERCENT` .

## <a name="cursors"></a>Курсоры

Kusto не поддерживает курсоры SQL.

## <a name="flow-control"></a>Управление потоком

Kusto не поддерживает операторы управления потоком, за исключением нескольких ограниченных случаев, таких как `IF` `THEN` `ELSE` предложение, имеющее идентичную схему `THEN` для `ELSE` пакетов и.

## <a name="data-types"></a>Типы данных

В зависимости от запроса возвращаемые данные могут иметь тип, отличный от типа в SQL Server.
Очевидным примером здесь являются такие типы, как `TINYINT` и `SMALLINT` , которые не имеют эквивалента в Kusto. Поэтому клиенты, которые предполагают значение типа `BYTE` или `INT16` могут получить `INT32` или `INT64` значение.

## <a name="column-order-in-results"></a>Порядок столбцов в результатах

Если в инструкции используется звездочкой `SELECT` , порядок столбцов в каждом результирующем наборе может отличаться от Kusto и SQL Server. В таких случаях клиент, использующий имена столбцов, будет работать лучше.
Если в инструкции нет звездочкой символа `SELECT` , то будут сохранены порядковые номера столбцов.

## <a name="columns-name-in-results"></a>Имена столбцов в результатах

В T-SQL несколько столбцов могут иметь одно и то же имя. Это запрещено в Kusto.
В случае конфликта имен имена столбцов могут отличаться в Kusto.
Однако исходное имя будет сохранено по крайней мере для одного из столбцов.

## <a name="any-all-and-exists-predicates"></a>Предикаты ANY, ALL и EXISTs

Kusto не поддерживает предикаты `ANY` , `ALL` и `EXISTS` .

## <a name="recursive-ctes"></a>Рекурсивные CTE

Kusto не поддерживает рекурсивные обобщенные табличные выражения.

## <a name="dynamic-sql"></a>Динамический SQL

Kusto не поддерживает динамические инструкции SQL (встроенное выполнение скрипта SQL, созданного запросом).

## <a name="within-group"></a>WITHIN GROUP

Kusto не поддерживает `WITHIN GROUP` предложение.

## <a name="truncate-function"></a>Truncate, функция

Функция Truncate (ODBC) в Kusto работает аналогично ROUND, что означает, что результатом будет ближайшее значение, а не меньшее из значений, возвращенного в SQL.