---
title: Управление политиками Инжестионтиме Kusto в Azure обозреватель данных
description: В этой статье описывается политика Инжестионтиме в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5ffc7ae648a9254564af0705cda84f3c79da99b
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966947"
---
# <a name="ingestiontime-policy-command"></a>команда политики времени приема

Политика Инжестионтиме — это необязательная политика, заданная для таблиц (она включена по умолчанию).
Он предоставляет примерное время приема записей в таблицу.

Доступ к значению времени приема можно получить во время выполнения запроса с помощью `ingestion_time()` функции.

```kusto
T | extend ingestionTime = ingestion_time()
```

Чтобы включить или отключить политику, выполните следующие действия.
```kusto
.alter table table_name policy ingestiontime true
```

Включение и отключение политики для нескольких таблиц:
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

Чтобы просмотреть политику, выполните следующие действия.
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

Чтобы удалить политику (равной отключенной), выполните следующие действия.
```kusto
.delete table table_name policy ingestiontime  
```