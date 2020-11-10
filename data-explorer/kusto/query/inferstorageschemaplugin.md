---
title: подключаемый модуль infer_storage_schema — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль infer_storage_schema в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 895f12799f4c44346af2b6b9be43bf98b7cf870e
ms.sourcegitcommit: e820a59191d2ca4394e233d51df7a0584fa4494d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94446214"
---
# <a name="infer_storage_schema-plugin"></a>Подключаемый модуль infer_storage_schema

Этот подключаемый модуль определяет схему внешних данных и возвращает ее в виде строки схемы CSL. Строку можно использовать при [создании внешних таблиц](../management/external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table).

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
  ],
  'DataFormat': 'parquet',
  'FileExtension': '.parquet'
});
evaluate infer_storage_schema(options)
```

## <a name="syntax"></a>Синтаксис

`evaluate` `infer_storage_schema(` *Параметры* `)`

## <a name="arguments"></a>Аргументы

Единственный аргумент *параметров* — это постоянное значение типа `dynamic` , которое содержит контейнер свойств, указывающий свойства запроса:

|Имя                    |Обязательно|Описание|
|------------------------|--------|-----------|
|`StorageContainers`|Да|Список [строк подключения к хранилищу](../api/connection-strings/storage.md) , представляющих URI префикса для хранимых артефактов данных|
|`DataFormat`|Да|Один из поддерживаемых [форматов данных](../../ingestion-supported-formats.md).|
|`FileExtension`|Нет|Проверять только те файлы, которые заканчиваются расширением этого файла. Это необязательно, но указание может ускорить процесс (или устранить проблемы с чтением данных).|
|`FileNamePrefix`|Нет|Проверять только файлы, начинающиеся с этого префикса. Это необязательно, но указание может ускорить процесс|
|`Mode`|Нет|Стратегия определения схемы, одна из следующих: `any` , `last` , `all` . Вывести схему данных из любого (первого найденного) файла, из последнего записанного файла или из всех файлов соответственно. Значение по умолчанию — `last`.|

## <a name="returns"></a>Возвращаемое значение

`infer_storage_schema`Подключаемый модуль возвращает одну таблицу результатов, содержащую одну строку или столбец со строкой схемы CSL.

> [!NOTE]
> * В дополнение к *чтению* секретные ключи URI контейнера хранилища должны иметь разрешения для *списка* .
> * Стратегия вывода схемы "все" является очень дорогостоящей операцией, так как она подразумевает чтение из *всех* обнаруженных артефактов и объединение их схемы.
> * Некоторые возвращаемые типы могут не быть реальными в результате неправильного подбора типа (или в результате процесса слияния схемы). Именно поэтому перед созданием внешней таблицы следует внимательно проанализировать результат.

## <a name="example"></a>Пример

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents;secretKey'
  ],
  'FileExtension': '.parquet',
  'FileNamePrefix': 'part-',
  'DataFormat': 'parquet'
});
evaluate infer_storage_schema(options)
```

*Результат*

|кслсчема|
|---|
|app_id: строка, user_id: long, event_time: DateTime, страна: строка, город: строка, device_type: строка, device_vendor: строка, ad_network: строка, кампания: строка, site_id: строка, event_type: строка, event_name: строка, согласованность: строка, days_from_install: int, доход: Real|

Используйте возвращенную схему в определении внешней таблицы:

```kusto
.create external table MobileEvents(
    app_id:string, user_id:long, event_time:datetime, country:string, city:string, device_type:string, device_vendor:string, ad_network:string, campaign:string, site_id:string, event_type:string, event_name:string, organic:string, days_from_install:int, revenue:real
)
kind=blob
partition by (dt:datetime = bin(event_time, 1d), app:string = app_id)
pathformat = ('app=' app '/dt=' datetime_pattern('yyyyMMdd', dt))
dataformat = parquet
(
    h@'https://storageaccount.blob.core.windows.net/MovileEvents;secretKey'
)
```