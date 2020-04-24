---
title: Управление политикой потокового ввоза - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление политикой потокового приема в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b0b1a76e52688dcc88ca87023309f9c970b4c702
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519624"
---
# <a name="streaming-ingestion-policy-management"></a>Управление политикой потокового приема

Политика потокового приема может быть прикреплена к базе данных или таблице, чтобы позволить потоковое проглатывание в этих местах. Политика также определяет рядные магазины, используемые для потокового приема.

Для получения дополнительной информации о потоковом проглатывания [см. Потоковое проглатительное (предварительное просмотр)](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming). Чтобы узнать больше о политике потокового приема, [см.](streamingingestionpolicy.md)

## <a name="show-policy-streamingingestion"></a>.show политики streamingingestion

Команда `.show policy streamingingestion` показывает политику потокового приема данных или таблицы.

**Синтаксис**

`.show``database` `policy` `streamingingestion` MyDatabase 
 `.show` `policy` `table` MyTable`streamingingestion`

**Возвращает**

Эта команда возвращает таблицу со следующими столбцов:

|Столбец    |Тип    |Описание
|---|---|---
|PolicyName|`string`|Название политики - StreamingIngestionPolicy
|EntityName|`string`|База данных или название таблицы
|Политика    |`string`|Объект JSON, определяющий политику потокового приема, отформатированный как [объект политики потокового вхады](#streaming-ingestion-policy-object)

**Пример**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|Политика|Детские лица|EntityType|
|---|---|---|---|---|
|StreamingIngestionPolicy|БД1|"NumberofRowStores": 4

### <a name="streaming-ingestion-policy-object"></a>Объект политики потоковой передачи

|Свойство  |Тип    |Описание                                                       |
|----------|--------|------------------------------------------------------------------|
|КоличествоOf-Магазинов |`int`  |Количество строк, назначенных объекту|
|SealIntervalLimit|`TimeSpan?`|Дополнительный предел для интервалов между операциями уплотнения на столе. Допустимый диапазон составляет от 1 до 24 часов. Значение по умолчанию — 24 часа.|
|SealThresholdBytes|`int?`|Дополнительный лимит для суммы данных, которые должны быть приняты для одной операции уплотнения на столе. Допустимый диапазон для значения составляет от 10 до 200 МБ. По умолчанию: 200 МБ.|

## <a name="alter-policy-streamingingestion"></a>.alter политики streamingingestion

Команда `.alter policy streamingingestion` устанавливает политику потоковой передачи базы данных или таблицы.

**Синтаксис**

* `.alter``database` MyDatabase `policy` `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``database` MyDatabase `policy` `streamingingestion``enable`

* `.alter``database` MyDatabase `policy` `streamingingestion``disable`

* `.alter``table` MyTable `policy` `streamingingestion` *StreamingIngestionPolicyObject*

* `.alter``table` MyTable `policy` `streamingingestion``enable`

* `.alter``table` MyTable `policy` `streamingingestion``disable`

**Примечания**

1. *StreamingIngestionPolicyObject* — объект JSON, в соответствии с которым определен объект политики потокового приема.

2. `enable`- установите политику потокового приема с 4 rowstores, если политика не определена или уже определена с 0 rowstores, в противном случае команда ничего не сделает.

3. `disable`- установите политику потокового приема с 0 rowstores, если политика уже определена с положительными rowstores, в противном случае команда ничего не сделает.

**Пример**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>.delete политики streamingingestion

Команда `.delete policy streamingingestion` удаляет политику потокового взорвателя из базы данных или таблицы.

**Синтаксис** 

`.delete``database` MyDatabase `policy``streamingingestion`

`.delete``table` MyTable `policy``streamingingestion`

**Возвращает**

Команда удаляет таблицу или объект потоковой политики, а затем возвращает вывод соответствующей команды [потоковой передачи политики .show.](#show-policy-streamingingestion)

**Пример**

```kusto
.delete database MyDatabase policy streamingingestion 
```