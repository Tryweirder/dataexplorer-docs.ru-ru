---
title: Ответ на запрос V2 HTTP - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается ответ Запроса V2 HTTP в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cca9b8381c7c59993c1e9071c46f34c1754d2429
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524316"
---
# <a name="query-v2-http-response"></a>Запрос V2 HTTP ответ

Если код состояния 200, то орган омовения — это массив JSON.
Каждый объект JSON в массиве называется _кадром._

Существует 7 типов кадров:

1. DataSetHeader
2. ТаблицаХедер
3. ТаблицаФрагмент
4. ТаблицаПрогресс
5. ТаблицаЗавершени
6. DataTable
7. DataSetCompletion

## <a name="datasetheader"></a>DataSetHeader 

Это всегда первый кадр в наборе данных и появляется ровно один раз.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

Где:

1. `Version`держит протокольную версию. Текущая версия — `v2.0`.
2. `IsProgressive`является флагом boolean, который указывает, содержит ли этот набор данных прогрессивные кадры. Прогрессивный кадр является одним из следующих:
    1. `TableHeader`: Содержит общую информацию о таблице
    2. `TableFragment`: Содержит осколок данных прямоугольники из таблицы
    3. `TableProgress`: Содержит прогресс в процентах (0-100)
    4. `TableCompletion`: Отмечает, что это последний кадр таблицы.
        
    Четыре кадра выше используются вместе для описания таблицы.
    Если этот флаг не установлен, то каждая таблица в наборе будет сериализована с помощью одного кадра:
      1. `DataTable`: Содержит всю информацию, необходимую клиенту об одной таблице в наборе данных.


## <a name="tableheader"></a>ТаблицаХедер

Запросы, которые выдаются с набором опции `results_progressive_enabled` на истину, могут включать этот кадр. Следуя этой таблице, клиентам следует ожидать `TableFragment` `TableProgress` переплетения последовательности и кадров, а затем кадра. `TableCompletion` После этого больше не будут отправлены кадры, связанные с этой таблицей.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

Где:

1. `TableId`является уникальным идентификатором таблицы.
2. `TableKind`является видом таблицы, который может быть одним из следующих:

      * ПервичныйРезультат
      * ЗапросЗавершеннаяИнформация
      * ЗапросTraceLog
      * КериПерфЛог
      * TableOfContents
      * QueryProperties
      * ЗапросПлан
      * Неизвестно
3. `TableName`— это название стола.
4. `Columns`представляет собой массив, описывающий схему таблицы:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
Поддерживаемые типы столбцов описаны [здесь](../../query/scalar-data-types/index.md).

## <a name="tablefragment"></a>ТаблицаФрагмент

Этот кадр содержит прямоугольный фрагмент данных таблицы. В дополнение к фактическим данным, `TableFragmentType` этот кадр содержит свойство, которое говорит клиенту, что делать с фрагментом (он может быть либо добавлен к существующим фрагментам, либо заменить их все вместе).

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

Где:

1. `TableId`является уникальным идентификатором таблицы.
2. `FieldCount`— количество столбцов в таблице
3. `TableFragmentType`описывает, что клиент должен делать с этим фрагментом. Может применяться один из перечисленных ниже типов.
      * DataAppend
      * Замена данных
4. `Rows`представляет собой двухмерный массив, содержащий данные фрагмента.

## <a name="tableprogress"></a>ТаблицаПрогресс

Этот кадр может переплеться с рамой, `TableFragment` описанной выше.
Единственной целью является уведомление клиента о ходе запроса.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

Где:

1. `TableId`является уникальным идентификатором таблицы.
2. `TableProgress`прогресс в процентах (0--100).

## <a name="tablecompletion"></a>ТаблицаЗавершени

На `TableCompletion` кадрах обозначается конец передачи таблицы. Больше никаких кадров, связанных с этой таблицей, не будет отправлено.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

Где:

1. `TableId`является уникальным идентификатором таблицы.
2. `RowCount`— это окончательное количество строк в таблице.

## <a name="datatable"></a>DataTable

Запросы, которые выдаются с флагом, `EnableProgressiveQuery` установленным на ложные, не будут включать ни один из предыдущих 4 кадров (,`TableHeader` `TableFragment` `TableProgress` и `TableCompletion`). Вместо этого каждая таблица в наборе данных `DataTable` будет передаваться с помощью одного кадра, кадра, который содержит всю информацию, необходимую клиенту для чтения таблицы.

```json
{
    "TableId": Number,

    "TableKind": string,

    "TableName": string,

    "Columns": Array,

    "Rows": Array,
}
```    

Где:

1. `TableId`является уникальным идентификатором таблицы.
2. `TableKind`является видом таблицы, который может быть одним из следующих:

      * ПервичныйРезультат
      * ЗапросЗавершеннаяИнформация
      * ЗапросTraceLog
      * КериПерфЛог
      * QueryProperties
      * ЗапросПлан
      * Неизвестно
3. `TableName`— это название стола.
4. `Columns`представляет собой массив, описывающий схему таблицы:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```
4. `Rows`представляет собой двухмерный массив, содержащий данные таблицы.

### <a name="the-meaning-of-tables-in-the-response"></a>Значение таблиц в ответе

* `PrimaryResult`- Основной таблик-результат запроса. Для каждого [табловидного выражения](../../query/tabularexpressionstatements.md)одна или несколько таблиц излучаются в порядке, представляя результаты, полученные оператором (таких таблиц может быть несколько из-за [пакетов](../../query/batches.md) и [операторов вилки).](../../query/forkoperator.md)
* `QueryCompletionInformation`- предоставляет дополнительную информацию о выполнении самого запроса, например, успешно ли он выполнен или нет, и какие ресурсы были использованы запросом (по аналогии с таблицей queryStatus в ответе v1). 
* `QueryProperties`- предоставляет дополнительные значения, такие как инструкции по визуализации клиентов (излучаемые, например, для отражения информации в [операторе рендеринга)](../../query/renderoperator.md)и информация [о курсоре базы данных).](../../management/databasecursor.md)
* `QueryTraceLog`- информация о журнале отслеживания производительности (возвращается при настройке perftrace в [свойствах запроса клиента).](../netfx/request-properties.md)

## <a name="datasetcompletion"></a>DataSetCompletion

Это окончательный кадр в наборе данных.
```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

Где:

1. `HasErrors`верно, если были какие-либо ошибки, генерирующие набор данных.
2. `Cancelled`верно, если запрос, приведем к генерации набора данных, был отменен на полпути. 
3. `OneApiErrors`передается только `HasErrors` в том случае, если это правда. Для описания формата, `OneApiErrors` см. [here](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md)