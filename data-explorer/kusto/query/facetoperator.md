---
title: оператор граней - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор граней в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b6c87fc044e0f00f77e28e85d89757a69835164
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515323"
---
# <a name="facet-operator"></a>Оператор facet

Возвращает набор таблиц, по одному для каждого указанного столбца.
Каждая таблица определяет список значений, принятых в столбце.
Дополнительная таблица может быть `with` создана с помощью оговорки.

**Синтаксис**

*T* `| facet by` *КолонкаИмя* `, ` -`with (` *filterPipe*`)`

**Аргументы**

* *КолонкаИмя:* Название столбца в входе, которое будет обобщено как таблица вывода.
* *filterPipe:* Выражение запроса применяется к таблице ввода для создания одного из выходов.

**Возвращает**

Несколько таблиц: `with` одна для положения и одна для каждого столба.

**Пример**

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```