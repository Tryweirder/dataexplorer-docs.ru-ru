---
title: Оператор externaldata в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор externaldata в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d46a8669c523955f74d3f489c7b10e5b0f7ccef6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373283"
---
# <a name="externaldata-operator"></a>Оператор externaldata

`externaldata`Оператор возвращает таблицу, схема которой определена в самом запросе и данные которой считываются из внешнего артефакта хранилища (например, большого двоичного объекта в хранилище BLOB-объектов Azure).

> [!NOTE]
> У этого оператора нет входных данных конвейера.

**Синтаксис**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Значение1* [ `,` ...] `)` ]

**Аргументы**

* *ColumnName*, *ColumnType*: определение схемы таблицы.
  Синтаксис совпадает с синтаксисом, используемым при определении таблицы в [таблице. Create](../management/create-table-command.md).

* *StorageConnectionString*: [строка подключения к хранилищу](../api/connection-strings/storage.md) описывает артефакт хранилища, содержащий возвращаемые данные.

* *Prop1*, *Значение1*,...: дополнительные свойства, описывающие способ интерпретации данных, полученных из хранилища, как указано в разделе [Свойства приема](../management/data-ingestion/index.md).
    * Поддерживаемые сейчас свойства: `format` и `ignoreFirstRecord` .
    * Поддерживаемые форматы данных: поддерживаются любые [форматы данных приема](../../ingestion-supported-formats.md) , включая,,, `csv` `tsv` `json` `parquet` , `avro` .

**Возвращает**

`externaldata`Оператор возвращает таблицу данных указанной схемы, данные из которой были проанализированы из указанного артефакта хранилища, указанного в строке подключения к хранилищу.

**Пример**

В следующем примере показано, как найти все записи в таблице, `UserID` столбец которой попадает в известный набор идентификаторов (по одному на строку) во внешнем большом двоичном объекте.
Так как запрос косвенно ссылается на этот набор, он может быть очень большим.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```