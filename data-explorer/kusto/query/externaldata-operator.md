---
title: Оператор внешних данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор внешних данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a4a151d1fd052e27e4daf76539ef6dbd9d94c83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515476"
---
# <a name="externaldata-operator"></a>Оператор externaldata

Оператор `externaldata` возвращает таблицу, схема которой определена в самом запросе и данные которой считывались из внешнего артефакта хранилища (например, капли в Azure Blob Storage).

> [!NOTE]
> Этот оператор не имеет ввода трубопровода.

**Синтаксис**

`externaldata``(` *КолонкаНалиподназвание* `:` *КолонкаТип* `,` `)` `]` `with` `(` `=` *Value1* `,` *Prop1* *StorageConnectionString* StorageConnectionString - Prop1 Value1 `[` `)`]

**Аргументы**

* *ColumnName*, *ColumnType*: Определите схему таблицы.
  Синтаксис такой же, как синтаксис, используемый при определении таблицы в [таблице .create.](../management/create-table-command.md)

* *StorageConnectionString*: [Строка подключения к хранению](../api/connection-strings/storage.md) описывает артефакт хранения, удерживающий данные для возврата.

* *Prop1*, *Value1*, ...: Дополнительные свойства, которые описывают, как интерпретировать данные, извлеченные из хранилища, как указано под [свойствами приема.](../management/data-ingestion/index.md)
    * В настоящее `format` `ignoreFirstRecord`время поддерживается свойства: и .
    * Поддерживаемые форматы данных: поддерживается любой из `csv` [форматов данных приема,](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) в том числе, `tsv`, `json`, `parquet`. `avro`

**Возвращает**

Оператор `externaldata` возвращает таблицу данных данной схемы, данные которой были разогнаны из указанного артефакта хранилища, указанного строкой подключения к хранилищу.

**Пример**

В следующем примере показано, как найти `UserID` все записи в таблице, столбец которой попадает в известный набор ивентов, удерживаемых (по одной на строку) во внешней капле.
Поскольку набор косвенно ссылается на запрос, он может быть очень большим.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```