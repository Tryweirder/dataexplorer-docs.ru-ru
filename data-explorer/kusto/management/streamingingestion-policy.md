---
title: Управление политиками приема потоковой передачи в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается управление политиками приема потоковой передачи в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 76844b764a8e21629c7d936f4c269d7d3ab1ec8c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373328"
---
# <a name="streaming-ingestion-policy-management"></a>Управление политиками приема потоковой передачи

Политику приема потоковой передачи можно присоединить к базе данных или таблице, чтобы разрешить прием потоковой передачи в эти расположения. Политика также определяет хранилища строк, используемые для приема потоковой передачи.

Дополнительные сведения о приеме потоковой передачи см. в разделе [потокового приема (Предварительная версия)](../../ingest-data-streaming.md). Дополнительные сведения о политике приема потоковой передачи см. в разделе [потоковая политика приема потоков](streamingingestionpolicy.md).

## <a name="show-policy-streamingingestion"></a>. показывать политику стреамингинжестион

`.show policy streamingingestion`Команда показывает политику приема потоковой передачи для базы данных или таблицы.

**Синтаксис**

`.show``database` `policy` `streamingingestion` 
 MyDatabase `.show` `table`MyTable `policy``streamingingestion`

**Возвращает**

Эта команда возвращает таблицу со следующими столбцами:

|Столбец    |Type    |Описание
|---|---|---
|PolicyName|`string`|Имя политики — Стреамингинжестионполици.
|EntityName|`string`|Имя базы данных или таблицы
|Политика    |`string`|Объект JSON, определяющий политику приема потоковой передачи, в формате [объект политики приема потоковой передачи](#streaming-ingestion-policy-object)

**Пример**

```kusto
.show database DB1 policy streamingingestion 
.show table T1 policy streamingingestion 
```

|PolicyName|EntityName|Политика|чилдентитиес|EntityType|
|---|---|---|---|---|
|стреамингинжестионполици|БД1|{"Нумберофровсторес": 4}

### <a name="streaming-ingestion-policy-object"></a>Объект политики приема потоковой передачи

|Свойство.  |Тип    |Описание                                                       |
|----------|--------|------------------------------------------------------------------|
|нумберофровсторес |`int`  |Число хранилищ строк, назначенных сущности|
|сеалинтерваллимит|`TimeSpan?`|Необязательный предел интервала между операциями запечатывания таблицы. Допустимый диапазон: от 1 до 24 часов. Значение по умолчанию — 24 часа.|
|сеалсрешолдбитес|`int?`|Необязательное ограничение объема данных для одной операции запечатывания таблицы. Допустимый диапазон значений — от 10 до 200 МБ. Значение по умолчанию: 200 МБ.|

## <a name="alter-policy-streamingingestion"></a>. ALTER Policy стреамингинжестион

`.alter policy streamingingestion`Команда задает политику стреамингинжестион для базы данных или таблицы.

**Синтаксис**

* `.alter``database` `policy` MyDatabase `streamingingestion` *Стреамингинжестионполициобжект*

* `.alter``database` `policy` MyDatabase `streamingingestion``enable`

* `.alter``database` `policy` MyDatabase `streamingingestion``disable`

* `.alter``table` `policy` MyTable `streamingingestion` *Стреамингинжестионполициобжект*

* `.alter``table` `policy` MyTable `streamingingestion``enable`

* `.alter``table` `policy` MyTable `streamingingestion``disable`

**Примечания**

1. *Стреамингинжестионполициобжект* — это объект JSON, в котором определен объект политики приема потоковой передачи.

2. `enable`— Задайте политику приема потоковой передачи с 4 ровсторес, если политика не определена или уже определена с 0 ровсторес, в противном случае команда не выполняет никаких действий.

3. `disable`— Задайте для политики приема потоковой передачи значение 0 ровсторес, если политика уже определена с положительным ровсторес, в противном случае команда не выполняет никаких действий.

**Пример**

```kusto
.alter database MyDatabase policy streamingingestion '{  "NumberOfRowStores": 4}'

.alter table MyTable policy streamingingestion '{  "NumberOfRowStores": 4}'
```

## <a name="delete-policy-streamingingestion"></a>. Удалите политику стреамингинжестион

`.delete policy streamingingestion`Команда удаляет политику стреамингинжестион из базы данных или таблицы.

**Синтаксис** 

`.delete``database`MyDatabase `policy``streamingingestion`

`.delete``table`MyTable `policy``streamingingestion`

**Возвращает**

Команда удаляет таблицу или объект политики стреамингинжестион базы данных, а затем возвращает выходные данные соответствующей команды [. показывать политику стреамингинжестион](#show-policy-streamingingestion) .

**Пример**

```kusto
.delete database MyDatabase policy streamingingestion 
```