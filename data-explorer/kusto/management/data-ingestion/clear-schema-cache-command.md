---
title: Очистка кэшированной схемы для приема потоковой передачи — Azure обозреватель данных
description: В этой статье описывается команда управления для очистки кэшированной схемы базы данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/20/2020
ms.openlocfilehash: 23d86e528dfe687b79ce225b16712184a9bfcde4
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784503"
---
# <a name="clear-schema-cache-for-streaming-ingestion"></a>Очистить кэш схемы для приема потоковой передачи

Узлы кластера. схема кэша баз данных, получающих данные посредством приема потоковой передачи. Этот процесс оптимизирует производительность и использование ресурсов кластера, но может привести к задержкам распространения при изменении схемы.
Очистите кэш, чтобы последующий запрос на прием потоковой передачи включил изменения схемы базы данных или таблицы.
Дополнительные сведения см. в разделе [потоковая передача и изменения схемы](streaming-ingestion-schema-changes.md).

**Очистить кэш схемы**

`.clear cache streamingingestion schema`Команда удаляет кэшированную схему со всех узлов кластера.

**Синтаксис**

`.clear``table` &lt; имя &gt; таблицы `cache` `streamingingestion``schema`

`.clear` `database` `cache` `streamingingestion` `schema`

**Возвращает**

Эта команда возвращает таблицу со следующими столбцами:

|Столбец    |Type    |Описание
|---|---|---
|NodeId|`string`|Идентификатор узла кластера
|Состояние|`string`|Успешно/не пройдено

**Пример**

```kusto
.clear database cache streamingingestion schema

.show table T1 cache streamingingestion schema
```

|NodeId|Состояние|
|---|---|
|Узел1|Успешно
|Узел2|Ошибка

> [!NOTE]
> Если команда завершается ошибкой или одна из строк в возвращенной таблице содержит *Status = Failed* , можно выполнить повторную попытку выполнения команды.
