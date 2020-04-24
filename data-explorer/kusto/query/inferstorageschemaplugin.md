---
title: infer_storage_schema плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается infer_storage_schema плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 6c4543a3b029017067867bb70d913509941c332e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513912"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schema плагин

Этот плагин выведет из себя схему внешних данных и возвращает ее в виде строки схемы CSL, которая может быть использована при [создании внешних таблиц.](../management/externaltables.md#create-or-alter-external-table)

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

Аргументом «Один *параметры»* является `dynamic` постоянное значение типа, в которой содержится пакет свойств, определяющий свойства запроса:

|Имя                    |Обязательно|Описание|
|------------------------|--------|-----------|
|`StorageContainers`|Да|Список [строк соединения хранилища, представляющих](../api/connection-strings/storage.md) префикс URI для сохраненных артефактов данных|
|`DataFormat`|Да|Один из [поддерживаемых форматов данных](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).|
|`FileExtension`|нет|Только файлы сканирования, заканчивающиеся этим расширением файла. Это не требуется, но указание может ускорить процесс (или устранить проблемы с чтением данных)|
|`FileNamePrefix`|нет|Только сканирование файлов, начиная с этой префикс. Это не требуется, но указание может ускорить процесс|
|`Mode`|нет|Стратегия выводов схемы, одна `any`из: , `last`. `all` Вывод о схеме данных из любого (первого найденного) файла, из последнего письменного файла или из всех файлов соответственно. Значение по умолчанию — `last`.|

**Возвращает**

Плагин `infer_storage_schema` возвращает одну таблицу результатов, содержащую одну строку/колонку, держащую строку схемы CSL.

> [!NOTE]
> * Стратегия выводов Schema 'все' очень "дорогой" операции, как это подразумевает чтение из *всех* артефактов, найденных и слияния их схемы.
> * Некоторые возвращенные типы могут быть не актуальными в результате неправильного угадывания типа (или, в результате процесса слияния схемы). Вот почему он советует тщательно просмотреть результат перед созданием внешней таблицы.

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

|ЧлШема|
|---|
|app_id:string, user_id:long, event_time:datetime, country:string, city:string, device_type:string, device_vendor:string, ad_network:string, campaign:string, site_id:string, event_type:string, event_name:string, organic:string, days_from_install:int, revenue:real|