---
title: Управление политиками Инжестионтиме Kusto в Azure обозреватель данных
description: Познакомьтесь с командами Инжестионтиме Policy в Azure обозреватель данных. Узнайте, как получить доступ к времени приема и как включить или отключить эту политику.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e1671373547a4ed069d67bc9153248f23bc3b5e
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003102"
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