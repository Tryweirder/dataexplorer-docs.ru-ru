---
title: Оператор externaldata в Azure обозреватель данных
description: В этой статье описывается оператор External Data в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 698acc481a6313160602c939774a6029978df483
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348111"
---
# <a name="externaldata-operator"></a>Оператор externaldata

`externaldata`Оператор возвращает таблицу, схема которой определена в самом запросе и данные которой считываются из внешнего артефакта хранилища, такого как большой двоичный объект в хранилище BLOB-объектов Azure.

## <a name="syntax"></a>Синтаксис

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Значение1* [ `,` ...] `)` ]

## <a name="arguments"></a>Аргументы

* *ColumnName*, *ColumnType*: аргументы определяют схему таблицы.
  Синтаксис совпадает с синтаксисом, используемым при определении таблицы в [таблице. Create](../management/create-table-command.md).

* *StorageConnectionString*: [строка подключения к хранилищу](../api/connection-strings/storage.md) описывает артефакт хранилища, содержащий возвращаемые данные.

* *Prop1*, *Значение1*,...: дополнительные свойства, описывающие способ интерпретации данных, полученных из хранилища, как указано в разделе [Свойства приема](../../ingestion-properties.md).
    * Поддерживаемые сейчас свойства: `format` и `ignoreFirstRecord` .
    * Поддерживаемые форматы данных: поддерживаются любые [форматы данных приема](../../ingestion-supported-formats.md) , включая,,, `CSV` `TSV` `JSON` `Parquet` , `Avro` .

> [!NOTE]
> У этого оператора нет входных данных конвейера.

## <a name="returns"></a>Возвращаемое значение

`externaldata`Оператор возвращает таблицу данных указанной схемы, данные которой были проанализированы из указанного артефакта хранилища, указанного в строке подключения к хранилищу.

## <a name="examples"></a>Примеры

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

> [!NOTE]
> Секционирование данных не распознается `externaldata()` оператором.
