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
ms.openlocfilehash: 907c6ddf84d772f800fce45d3c1245bbd11b0c85
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616462"
---
# <a name="ingestiontime-policy"></a>Политика времени приема

Политика Инжестионтиме — это необязательная политика, заданная для таблиц (она включена по умолчанию).
Он предоставляет примерное время приема записей в таблицу.

Доступ к значению времени приема можно получить во время выполнения `ingestion_time()` запроса с помощью функции.

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