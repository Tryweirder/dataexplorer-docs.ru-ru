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
ms.openlocfilehash: 8bb30180a7506b594e5747e3591f0d1aff80f8c3
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557366"
---
# <a name="externaldata-operator"></a>Оператор externaldata

`externaldata`Оператор возвращает таблицу, схема которой определена в самом запросе, а данные считываются из внешнего артефакта хранилища, такого как большой двоичный объект в хранилище BLOB-объектов Azure или файл в Azure Data Lake Storage.

## <a name="syntax"></a>Синтаксис

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...]`)`   
`[`*StorageConnectionString* [ `,` ...]`]`   
[ `with` `(` *PropertyName* `=` *propertyvalue* [ `,` ...] `)` ]

## <a name="arguments"></a>Аргументы

* *ColumnName*, *ColumnType*: аргументы определяют схему таблицы.
  Синтаксис совпадает с синтаксисом, используемым при определении таблицы в [таблице. Create](../management/create-table-command.md).

* *StorageConnectionString*: [строки подключения к хранилищу](../api/connection-strings/storage.md) , описывающие артефакты хранилища, содержащие возвращаемые данные.

* *PropertyName*, *propertyvalue*,...: дополнительные свойства, описывающие способ интерпретации данных, полученных из хранилища, как указано в разделе [Свойства приема](../../ingestion-properties.md).

В настоящее время поддерживаются следующие свойства:

| Свойство         | Тип     | Описание       |
|------------------|----------|-------------------|
| `format`         | `string` | Формат данных. Если не указано, предпринимается попытка определить формат данных из расширения файла (по умолчанию — `CSV` ). Поддерживается любой из [форматов данных приема](../../ingestion-supported-formats.md) . |
| `ignoreFirstRecord` | `bool` | Если задано значение true, то указывает, что первая запись в каждом файле игнорируется. Это свойство полезно при запросе CSV-файлов с заголовками. |
| `ingestionMapping` | `string` | Строковое значение, указывающее, как сопоставлять данные из исходного файла с фактическими столбцами в результирующем наборе операторов. Дополнительные сведения см. в разделе [о сопоставлении данных](../management/mappings.md). |


> [!NOTE]
> * Этот оператор не принимает входные данные конвейера.
> * [Ограничения стандартных запросов](../concepts/querylimits.md) также применяются к запросам внешних данных.

## <a name="returns"></a>Возвращаемое значение

`externaldata`Оператор возвращает таблицу данных указанной схемы, данные которой были проанализированы из указанного артефакта хранилища, указанного в строке подключения к хранилищу.

## <a name="examples"></a>Примеры

**Получение списка идентификаторов пользователей, хранящихся в хранилище BLOB-объектов Azure**

В следующем примере показано, как найти все записи в таблице, `UserID` столбец которой попадает в известный набор идентификаторов (по одному на строку) во внешнем файле хранилища. Так как формат данных не указан, в качестве формата данных используется формат `TXT` .

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt" 
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

**Запрос нескольких файлов данных**

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
> Секционирование данных не распознается `externaldata` оператором.

**Запросы к иерархическим форматам данных**

Для запроса к иерархическим форматам данных, таким как `JSON` ,, `Parquet` `Avro` или `ORC` , `ingestionMapping` необходимо указать в свойствах оператора. В этом примере файл JSON хранится в хранилище BLOB-объектов Azure со следующим содержимым:

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

Чтобы запросить этот файл с помощью `externaldata` оператора, необходимо указать сопоставление данных. Сопоставление определяет способ сопоставления полей JSON с столбцами результирующего набора оператора:

```kusto
externaldata(Timestamp: datetime, TenantId: guid, MethodName: string)
[ 
   h@'https://mycompanystorage.blob.core.windows.net/events/2020/09/01/part-0000046c049c1-86e2-4e74-8583-506bda10cca8.json?...SAS...'
]
with(format='multijson', ingestionMapping='[{"Column":"Timestamp","Properties":{"Path":"$.time"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]')
```

`MultiJSON`Формат используется здесь, так как отдельные записи JSON разбиваются на несколько строк.

Дополнительные сведения о синтаксисе сопоставления см. в разделе [сопоставления данных](../management/mappings.md).
