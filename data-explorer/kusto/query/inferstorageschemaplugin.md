---
title: подключаемый модуль infer_storage_schema — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль infer_storage_schema в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 5c430fd0ca18265c5800165b33bfe14126aee02a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373272"
---
# <a name="infer_storage_schema-plugin"></a>подключаемый модуль infer_storage_schema

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

**Синтаксис**

`evaluate` `infer_storage_schema(` *Параметры* `)`

**Аргументы**

Единственный аргумент *параметров* — это постоянное значение типа `dynamic` , которое содержит контейнер свойств, указывающий свойства запроса:

|Имя                    |Обязательно|Описание|
|------------------------|--------|-----------|
|`StorageContainers`|Да|Список [строк подключения к хранилищу](../api/connection-strings/storage.md) , представляющих URI префикса для хранимых артефактов данных|
|`DataFormat`|Да|Один из поддерживаемых [форматов данных](../../ingestion-supported-formats.md).|
|`FileExtension`|нет|Проверять только те файлы, которые заканчиваются расширением этого файла. Это необязательно, но указание может ускорить процесс (или устранить проблемы с чтением данных).|
|`FileNamePrefix`|нет|Проверять только файлы, начинающиеся с этого префикса. Это необязательно, но указание может ускорить процесс|
|`Mode`|нет|Стратегия определения схемы, одна из следующих: `any` , `last` , `all` . Вывести схему данных из любого (первого найденного) файла, из последнего записанного файла или из всех файлов соответственно. Значение по умолчанию — `last`.|

**Возвращает**

`infer_storage_schema`Подключаемый модуль возвращает одну таблицу результатов, содержащую одну строку или столбец со строкой схемы CSL.

> [!NOTE]
> * Стратегия вывода схемы "все" является очень дорогостоящей операцией, так как она подразумевает чтение из *всех* обнаруженных артефактов и объединение их схемы.
> * Некоторые возвращаемые типы могут не быть реальными в результате неправильного подбора типа (или в результате процесса слияния схемы). Именно поэтому перед созданием внешней таблицы следует внимательно проанализировать результат.

**Пример**

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents/2015;secretKey'
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