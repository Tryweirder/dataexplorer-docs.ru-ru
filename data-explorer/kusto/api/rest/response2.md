---
title: HTTP-ответ на запрос v2 в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается HTTP-ответ запроса v2 в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 86a56d77005b2c6b5c9d38bbec85eebfbcb481dc
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617907"
---
# <a name="query-v2-http-response"></a>HTTP-ответ запроса v2

Если код состояния — 200, то текст ответа является массивом JSON.
Каждый объект JSON в массиве называется _кадром_.

Существует несколько типов кадров:

* [датасесеадер](#datasetheader)
* [таблехеадер](#tableheader)
* [таблефрагмент](#tablefragment)
* [таблепрогресс](#tableprogress)
* [таблекомплетион](#tablecompletion)
* [DataTable](#datatable)
* [датасеткомплетион](#datasetcompletion)

## <a name="datasetheader"></a>датасесеадер 

`DataSetHeader` Фрейм всегда является первым в наборе данных и отображается ровно один раз.

```json
{
    "Version": string,
    "IsProgressive": Boolean
}
```

Где:

* `Version`Версия протокола. Текущая версия — `v2.0`.
* `IsProgressive`логический флаг, указывающий, содержит ли этот набор данных прогрессивные кадры. 
   Прогрессивный кадр является одним из следующих:
   
     | Frame             | Описание                                    |
     |-------------------| -----------------------------------------------|
     | `TableHeader`     | Содержит общие сведения о таблице   |
     | `TableFragment`   | Содержит прямоугольный сегмент данных таблицы |
     | `TableProgress`   | Содержит ход выполнения в процентах (0-100)       |
     | `TableCompletion` | Указывает, что этот кадр является последним      |
        
    Приведенные выше кадры описывают таблицу.
    Если `IsProgressive` флаг не имеет значение true, каждая таблица в наборе будет сериализована с использованием одного кадра:
* `DataTable`: Содержит все сведения, необходимые клиенту относительно одной таблицы в наборе данных.

## <a name="tableheader"></a>таблехеадер

Запросы, выполняемые с `results_progressive_enabled` параметром, имеющим значение true, могут включать этот кадр. После этой таблицы Клиенты могут заждать последовательной последовательности кадров `TableFragment` и `TableProgress` . Окончательный кадр таблицы — `TableCompletion`.

```json
{
    "TableId": Number,
    "TableKind": string,
    "TableName": string,
    "Columns": Array,
}
```

Где:

* `TableId`уникальный идентификатор таблицы.
* `TableKind`является одним из следующих:

    * примариресулт
    * куерикомплетионинформатион
    * куеритрацелог
    * куериперфлог
    * TableOfContents
    * QueryProperties
    * куериплан
    * Неизвестно
      
* `TableName`имя таблицы.
* `Columns`— Это массив, описывающий схему таблицы.

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

Поддерживаемые типы столбцов описаны [здесь](../../query/scalar-data-types/index.md).

## <a name="tablefragment"></a>таблефрагмент

`TableFragment` Кадр содержит прямоугольный фрагмент данных таблицы. Помимо фактических данных, этот кадр также содержит `TableFragmentType` свойство, которое сообщает клиенту, что нужно сделать с фрагментом. Фрагмент добавляется к существующим фрагментам или заменяет их.

```json
{
    "TableId": Number,
    "FieldCount": Number,
    "TableFragmentType": string,
    "Rows": Array
}
```

Где:

* `TableId`уникальный идентификатор таблицы.
* `FieldCount`число столбцов в таблице.
* `TableFragmentType`Описывает, что должен делать клиент с этим фрагментом. 
    `TableFragmentType`является одним из следующих:
    
    * Append
    * Замена
      
* `Rows`Двумерный массив, содержащий данные фрагмента.

## <a name="tableprogress"></a>таблепрогресс

`TableProgress` Кадр может находиться в чередовании `TableFragment` с описанным выше кадром.
Его единственная цель — уведомить клиента о ходе выполнения запроса.

```json
{
    "TableId": Number,
    "TableProgress": Number,
}
```

Где:

* `TableId`уникальный идентификатор таблицы.
* `TableProgress`ход выполнения в процентах (0--100).

## <a name="tablecompletion"></a>таблекомплетион

`TableCompletion` Кадр отмечает конец передачи таблицы. Дополнительные кадры, связанные с этой таблицей, не будут отправлены.

```json
{
    "TableId": Number,
    "RowCount": Number,
}
```    

Где:

* `TableId`уникальный идентификатор таблицы.
* `RowCount`Общее число строк в таблице.

## <a name="datatable"></a>DataTable

`EnableProgressiveQuery` Запросы, выданные с флагом, установленным в значение false, не будут содержать`TableHeader`кадры `TableFragment`( `TableProgress`,, `TableCompletion`и). Вместо этого каждая таблица в наборе данных будет передаваться с `DataTable` помощью кадра, содержащего все необходимые клиенту сведения для чтения таблицы.

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

* `TableId`уникальный идентификатор таблицы.
* `TableKind`является одним из следующих:

    * примариресулт
    * куерикомплетионинформатион
    * куеритрацелог
    * куериперфлог
    * QueryProperties
    * куериплан
    * Неизвестно
      
* `TableName`имя таблицы.
* `Columns`— Это массив, описывающий схему таблицы и включающий:

```json
{
    "ColumnName": string,
    "ColumnType": string,
}
```

* `Rows`Двумерный массив, содержащий данные таблицы.

### <a name="the-meaning-of-tables-in-the-response"></a>Значение таблиц в ответе

* `PrimaryResult`— Основной табличный результат запроса. Для каждой [инструкции табличного выражения](../../query/tabularexpressionstatements.md)создается одна или несколько таблиц в порядке, представляющих результаты, сформированные инструкцией. Из-за [пакетов](../../query/batches.md) и [операторов ветвления](../../query/forkoperator.md)может существовать несколько таких таблиц.
* `QueryCompletionInformation`— Предоставляет дополнительные сведения о выполнении самого запроса, например об успешном завершении или отсутствии, а также о том, какие ресурсы использовались запросом (аналогично таблице QueryStatus в ответе v1). 
* `QueryProperties`— Предоставляет дополнительные значения, такие как инструкции по визуализации клиента (например, для отражения сведений в [операторе Render](../../query/renderoperator.md)) и сведения о [курсоре базы данных](../../management/databasecursor.md) .
* `QueryTraceLog`— Сведения журнала трассировки производительности (возвращаются, если `perftrace` в [свойствах клиентского запроса](../netfx/request-properties.md) установлено значение true).

## <a name="datasetcompletion"></a>датасеткомплетион

`DataSetCompletion` Кадр является последним в наборе данных.

```json
{
    "HasErrors": Boolean,
    "Cancelled": Boolean,
    "OneApiErrors": Array,
}
```

Где:

* `HasErrors`имеет значение true, если при создании набора данных возникли ошибки.
* `Cancelled`имеет значение true, если запрос, который привел к формированию набора данных, был отменен до завершения. 
* `OneApiErrors`возвращается, только если `HasErrors` имеет значение true. Описание формата см. в `OneApiErrors` разделе 7.10.2 [здесь](https://github.com/Microsoft/api-guidelines/blob/vNext/Guidelines.md).
