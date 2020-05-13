---
title: оператор Print — Azure обозреватель данных
description: В этой статье описывается оператор Print в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: d5788ee937fe110b63a8f137fdab0790eb7cb37e
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373206"
---
# <a name="print-operator"></a>Оператор print

Выводит одну строку с одним или несколькими скалярными выражениями.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**Синтаксис**

`print`[*ColumnName* `=` ] *Скаларекспрессион* [","...]

**Аргументы**

* *ColumnName*: имя параметра, присваиваемое единственному столбцу вывода.
* *Скаларекспрессион*: скалярное выражение для вычисления.

**Возвращает**

Однострочная таблица с одним столбцом, одна ячейка которой имеет значение вычисленного *скаларекспрессион*.

**Примеры**

`print`Оператор полезен в качестве быстрого способа вычисления одного или нескольких скалярных выражений и создания таблицы с одной строкой из результирующих значений.
Пример:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print banner=strcat("Hello", ", ", "World!")
```
