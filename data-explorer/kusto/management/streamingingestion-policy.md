---
title: Управление политиками приема потоковой передачи Kusto — Azure обозреватель данных
description: В этой статье описывается управление политиками приема потоковой передачи в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: ff13ec8fcce49f4d92212e6a38797a97f9ea9dd6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321256"
---
# <a name="streaming-ingestion-policy-command"></a>Команда политики приема потоков

Политику приема потоковой передачи можно задать для таблицы, чтобы разрешить прием потоковой передачи в эту таблицу. Политику также можно установить на уровне базы данных, чтобы применить тот же параметр к текущим и будущим таблицам.

Дополнительные сведения см. в разделе [потокового приема](../../ingest-data-streaming.md). Дополнительные сведения о политике приема потоковой передачи см. в разделе [потоковая политика приема потоков](streamingingestionpolicy.md).

## <a name="display-the-policy"></a>Отображение политики

`.show policy streamingingestion`Команда показывает политику приема потоковой передачи для базы данных или таблицы.
 
**Синтаксис**

`.show``{database|table}` &lt; имя &gt; сущности `policy``streamingingestion`

**Возвращает**

Эта команда возвращает таблицу со следующими столбцами:

|Столбец    |Type    |Описание
|---|---|---
|PolicyName|`string`|Имя политики — Стреамингинжестионполици.
|EntityName|`string`|Имя базы данных или таблицы
|Политика    |`string`|[объект политики приема потоковой передачи](#streaming-ingestion-policy-object)

**Примеры**

```kusto
.show database DB1 policy streamingingestion

.show table T1 policy streamingingestion
```

|PolicyName|EntityName|Политика|чилдентитиес|EntityType|
|---|---|---|---|---|
|стреамингинжестионполици|БД1|{"Onenableed": true, "Хинталлокатедрате": NULL}

## <a name="change-the-policy"></a>изменить политику;

`.alter[-merge] policy streamingingestion`Семейство команд предоставляет средства для изменения политики приема потоковой передачи базы данных или таблицы.

**Синтаксис**

* `.alter``{database|table}` &lt; имя &gt; сущности `policy` `streamingingestion``[enable|disable]`

* `.alter``{database|table}` &lt; &gt; `policy` `streamingingestion` &lt; [объект политики приема потоков](#streaming-ingestion-policy-object) имен сущностей&gt;

* `.alter-merge``{database|table}` &lt; &gt; `policy` `streamingingestion` &lt; [объект политики приема потоков](#streaming-ingestion-policy-object) имен сущностей&gt;

> [!Note]
>
> * Позволяет изменить включенное или отключенное состояние приема потоковой передачи без изменения других свойств политики или установки свойств на значения по умолчанию, если политика ранее не была определена для сущности.
>
> * Позволяет заменить всю политику приема потоковой передачи для сущности. [объект политики приема потоковой передачи](#streaming-ingestion-policy-object) должен включать все обязательные свойства.
>
> * Позволяет заменять только указанные свойства политики приема потоковой передачи для сущности. [Объект политики приема потоковой передачи](#streaming-ingestion-policy-object) может включать некоторые или никакие из обязательных свойств.

**Возвращает**

Команда изменяет таблицу или объект политики базы данных `streamingingestion` , а затем возвращает выходные данные соответствующей [ `.show policy` `streamingingestion` ](#display-the-policy) команды.

**Примеры**

```kusto
.alter database DB1 policy streamingingestion enable

.alter table T1 policy streamingingestion disable

.alter database DB1 policy streamingingestion '{"IsEnabled": true, "HintAllocatedRate": 2.1}'

.alter table T1 policy streamingingestion '{"IsEnabled": true}'

.alter-merge database DB1 policy streamingingestion '{"IsEnabled": false}'

.alter-merge table T1 policy streamingingestion '{"HintAllocatedRate": 1.5}'
```

## <a name="delete-the-policy"></a>Удаление политики

`.delete policy streamingingestion`Команда удаляет политику стреамингинжестион из базы данных или таблицы.

**Синтаксис**

`.delete``{database|table}` &lt; имя &gt; сущности `policy``streamingingestion`

**Возвращает**

Команда удаляет таблицу или объект политики стреамингинжестион базы данных, а затем возвращает выходные данные соответствующей [`.show policy streamingingestion`](#display-the-policy) команды.

**Примеры**

```kusto
.delete database DB1 policy streamingingestion

.delete table T1 policy streamingingestion
```

### <a name="streaming-ingestion-policy-object"></a>Объект политики приема потоковой передачи

При вводе и выходе команд управления объект политики приема потоков представляет собой строку в формате JSON, которая включает следующие свойства.

|Свойство|Тип|Описание|Обязательный/необязательный
|---|---|---|---
|IsEnabled|`bool`|Включено приема потоков для сущности| Обязательно
|хинталлокатедрате|`double`|Предполагаемая скорость передает данных в ГБ/час|Необязательно
