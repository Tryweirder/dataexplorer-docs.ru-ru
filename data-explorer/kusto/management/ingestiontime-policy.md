---
title: Политика IngestionTime - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается политика IngestionTime в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c42da570b961595be1fbcae352fe121d8b6f59ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520899"
---
# <a name="ingestiontime-policy"></a>Политика времени приема

Политика IngestionTime — это дополнительная политика, установленная в таблицах (она включена по умолчанию).
он обеспечивает приблизительное время приема записей в таблицу.

Значение времени заглаживания можно получить во `ingestion_time()` время запроса с помощью функции.

```kusto
T | extend ingestionTime = ingestion_time()
```

Для включения/отключать политику:
```kusto
.alter table table_name policy ingestiontime true
```

Для включения/отключать политику нескольких таблиц:
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

Для просмотра политики:
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

Чтобы удалить политику (равно отключению):
```kusto
.delete table table_name policy ingestiontime  
```