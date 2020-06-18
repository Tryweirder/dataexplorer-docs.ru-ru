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
ms.openlocfilehash: f8878a3c4589dca3cfacf935a787e8c754ab3ede
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84942681"
---
# <a name="externaldata-operator"></a>Оператор externaldata

`externaldata`Оператор возвращает таблицу, схема которой определена в самом запросе и данные которой считываются из внешнего артефакта хранилища (например, большого двоичного объекта в хранилище BLOB-объектов Azure).

**Синтаксис**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Значение1* [ `,` ...] `)` ]

**Аргументы**

* *ColumnName*, *ColumnType*: определение схемы таблицы.
  Синтаксис совпадает с синтаксисом, используемым при определении таблицы в [таблице. Create](../management/create-table-command.md).

* *StorageConnectionString*: [строка подключения к хранилищу](../api/connection-strings/storage.md) описывает артефакт хранилища, содержащий возвращаемые данные.

* *Prop1*, *Значение1*,...: дополнительные свойства, описывающие способ интерпретации данных, полученных из хранилища, как указано в разделе [Свойства приема](../../ingestion-properties.md).
    * Поддерживаемые сейчас свойства: `format` и `ignoreFirstRecord` .
    * Поддерживаемые форматы данных: поддерживаются любые [форматы данных приема](../../ingestion-supported-formats.md) , включая,,, `CSV` `TSV` `JSON` `Parquet` , `Avro` .

> [!NOTE]
> У этого оператора нет входных данных конвейера.

**Возвращает**

`externaldata`Оператор возвращает таблицу данных указанной схемы, данные из которой были проанализированы из указанного артефакта хранилища, указанного в строке подключения к хранилищу.

**Примеры**

В следующем примере показано, как найти все записи в таблице, `UserID` столбец которой попадает в известный набор идентификаторов (по одному на строку) во внешнем большом двоичном объекте.
Так как запрос косвенно ссылается на этот набор, он может быть большим.

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

В следующем примере выполняется запрос нескольких файлов данных, хранящихся во внешнем хранилище.

```kusto
externaldata(Timestamp:datetime, ProductId:string, ProductDescription:string)
[
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/03/part-00000-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz?...SAS..."
]
with(format="csv")
| summarize count() by ProductId
```

Приведенный выше пример можно рассматривать как быстрый способ запроса нескольких файлов данных без определения [внешней таблицы](schema-entities/externaltables.md). 

>[!NOTE]
>Оператор не распознает секционирование данных `externaldata()` .
