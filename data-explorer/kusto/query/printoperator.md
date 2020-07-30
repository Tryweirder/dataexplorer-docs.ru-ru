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
ms.openlocfilehash: 9a5a780a6f7bdf277566d1c0421c5ca2a3a93602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346071"
---
# <a name="print-operator"></a>Оператор print

Выводит одну строку с одним или несколькими скалярными выражениями.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

## <a name="syntax"></a>Синтаксис

`print`[*ColumnName* `=` ] *Скаларекспрессион* [","...]

## <a name="arguments"></a>Аргументы

* *ColumnName*: имя параметра, присваиваемое единственному столбцу вывода.
* *Скаларекспрессион*: скалярное выражение для вычисления.

## <a name="returns"></a>Результаты

Однострочная таблица с одним столбцом, одна ячейка которой имеет значение вычисленного *скаларекспрессион*.

## <a name="examples"></a>Примеры

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
