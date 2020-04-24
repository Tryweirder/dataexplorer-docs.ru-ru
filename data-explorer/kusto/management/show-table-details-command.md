---
title: Подробная информация о таблице .show - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны сведения о таблице .show в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 6197e173df417acb1f5dc506337773f9534564bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519794"
---
# <a name="show-table-details"></a>детали таблицы .show
Возвращает набор, содержащий указанную таблицу или все таблицы в базе данных с подробным резюме свойств каждой таблицы.

Требуется [разрешение просмотра базы данных.](../management/access-control/role-based-authorization.md)

```
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**Вывод**

| Выходной параметр           | Тип     | Описание                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | Строка   | Имя таблицы.                                                                          |
| `DatabaseName`             | Строка   | База данных, к которой принадлежит таблица.                                                         |
| `Folder`                   | Строка   | Папка стола.                                                                             |
| `DocString`                | Строка   | Строка, документирующая таблицу.                                                                 |
| `TotalExtents`             | Int64    | Общее количество уровней в таблице.                                                       |
| `TotalExtentSize`          | Double   | Общий размер размеров (сжатый размер и размер индекса) в таблице.                          |
| `TotalOriginalSize`        | Double   | Общий исходный размер данных в таблице.                                                   |
| `TotalRowCount`            | Int64    | Общее количество строк в таблице.                                                          |
| `HotExtents`               | Int64    | Общее количество уровней в таблице, хранящихся в горячем кэше.                              |
| `HotExtentSize`            | Double   | Общий размер размеров (сжатый размер и размер индекса) в таблице, хранящийся в горячем кэше. |
| `HotOriginalSize`          | Double   | Общий исходный размер данных в таблице, хранящийся в горячем кэше.                          |
| `HotRowCount`              | Int64    | Общее количество строк в таблице, хранящихся в горячем кэше.                                 |
| `AuthorizedPrincipals`     | Строка   | Авторизованные принципы таблицы, серийные как JSON.                                          |
| `RetentionPolicy`          | Строка   | Эффективная`*` политика удержания таблицы, серизированная как JSON.                                  |
| `CachingPolicy`            | Строка   | Эффективная`*` политика кэширования таблицы, серибная как JSON.                                    |
| `ShardingPolicy`           | Строка   | Эффективная`*` политика осколков стола, сериализованная как JSON.666666666666666666666666666                     |
| `MergePolicy`              | Строка   | Эффективная`*` политика слияния таблицы, серизированная как JSON.                                      |
| `StreamingIngestionPolicy` | Строка   | Эффективная`*` политика потокового приема, сериализованная как JSON.                        |
| `MinExtentsCreationTime`   | Дата и время | Минимальное время создания степени в таблице (или нулевых, если нет размеров).         |
| `MaxExtentsCreationTime`   | Дата и время | Максимальное время создания степени в таблице (или нулевых, если нет размеров).         |
| `RowOrderPolicy`           | Строка   | Эффективная политика заказа строки таблицы, серизированная как JSON.                                     |

`*`*Принимая во внимание политику родительских организаций (таких как база данных/кластер).*

**Пример выходных данных**

| TableName         | имя_базы_данных | Папка | DocString | TotalExtents | TotalExtentSize | TotalOriginalSize | TotalRowCount | HotExtents | HotExtentSize | HotOriginalSize | HotRowCount | УполномоченныеПринципы                                                                                                                                                                               | СохранениеПолитика                                                                                                                                       | КэшингПолитика                                                                        | ShardingПолитика                                                                    | СлияниеПолитика                                                                                                                                             | StreamingIngestionPolicy | MinExtentsСозданиеВремя      | MaxExtentsCreationTime      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| Операции        | Операции   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | «Тип»: «Пользователь AAD», «DisplayName»: «Мое имя (upn:)», alias@fabrikam.com"ObjectId": "a7a7777-4c21-4649-95c5-350bf486087b", "F'N": "aduser'a7a777777-4c21-4649-95c5-350bf48087b", "Aduser'a7a777777-4c21-4649-95c5-350bf48087b", "Aduser"" | "SoftDeletePeriod": "365.00:00:00", "ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0  | "DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": | "MaxrowCount": 750000, "MaxExtentSizeinMb": 1024, "MaxOriginalSizeinMb": 2048 | "RowCountUpperBoundForMerge": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "AllowMerge": True | null                     |
| ServiceOperations | Операции   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | «Тип»: «Пользователь AAD», «DisplayName»: «Мое имя (upn:)», alias@fabrikam.com"ObjectId": "a7a7777-4c21-4649-95c5-350bf486087b", "F'N": "aduser'a7a777777-4c21-4649-95c5-350bf48087b", "Aduser'a7a777777-4c21-4649-95c5-350bf48087b", "Aduser"" | "SoftDeletePeriod": "365.00:00:00", "ContainerRecyclingPeriod": "1.00:00:00", "ExtentsDataSizeLimitInBytes": 0, "OriginalDataSizeLimitInBytes": 0 | "DataHotSpan": "4.00:00:00", "IndexHotSpan": "4.00:00:00", "ColumnOverrides": | "MaxrowCount": 750000, "MaxExtentSizeinMb": 1024, "MaxOriginalSizeinMb": 2048 | "RowCountUpperBoundForMerge": 0, "MaxExtentsToMerge": 100, "LoopPeriod": "01:00:00", "MaxRangeInHours": 3, "AllowRebuild": true, "AllowMerge": True | null                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |