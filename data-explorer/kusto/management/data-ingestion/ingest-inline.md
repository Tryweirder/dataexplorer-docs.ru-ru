---
title: Вливая команда .ingest (толчок) - Проводник данных Azure (ru) Документы Майкрософт
description: В этой статье описывается влиная команда .ingest (push) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 47da2df6ff974afb5e91224ade695dc0863b6817
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521375"
---
# <a name="the-ingest-inline-command-push"></a>Вонная команда .ingest (толчок)

Эта команда попадает в таблицу, "толкая" данные, встроенные в сам текст команды.

> [!NOTE]
> Основная цель этой команды предназначена для ручных специальных целей тестирования.
> Для использования в производстве рекомендуется использовать другие методы приема, более подходящие для массовой доставки огромных объемов данных, таких как [глотание из хранилища.](./ingest-from-storage.md)

**Синтаксис**

`.ingest``inline` `with` `(` `,` `=` *IngestionPropertyValue* *TableName* *IngestionPropertyName* ТаблицаИмя - IngestionPropertyName IngestionPropertyValue `into` `table` `)` `<|` *Данные*



**Аргументы**

* *TableName* — это название таблицы для того, чтобы глотать данные.
  Имя таблицы всегда относительно базы данных в контексте, и ее схема представляет собой схему, которая будет предполагать для данных, если не предусмотрен объект отображения схемы.

* *Данные* — это содержимое данных для улавливания. Если иное не изменено свойствами приема, это содержимое разбирается как CSV.
  Обратите внимание, что в отличие от большинства команд управления и запросов, текст части *данных* команды не должен следовать синтаксическим конвенциям `//` языка (например, символы whitespace важны, комбинация не рассматривается как комментарий и т.д.)

* *IngestionPropertyName*, *IngestionPropertyValue*: Любое количество [свойств приема,](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) которые влияют на процесс приема.

**Результаты**

Результатом команды является таблица с таким количеством записей, сколько и осколков данных ("объемы"), генерируемых командой.
Если не были созданы осколки данных, одна запись возвращается с пустым (нулевой стоимостью) идентификатором размера.

|Имя       |Тип      |Описание                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |Уникальный идентификатор для осколок данных, сгенерированный командой.|

**Примеры**

Следующая команда глотает данные`Purchases`в таблицу `SKU` () `string`с `Quantity` двумя столбцов, (типа) и (типа). `long`

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```



<!--
It is possible to generate inline ingests commands using the Kusto.Data client library. (Note that compression does allow one to embed newlines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->