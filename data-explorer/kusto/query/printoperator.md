---
title: оператор печати - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор печати в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: b751b07446a74ef17002abac26e4c881a2da757b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510971"
---
# <a name="print-operator"></a>Оператор print

Выводы одного ряда с одним или более масштабными выражениями.

```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**Синтаксис**

`print`*КолонкаИмя* `=` *ScalarВыражение* »,''...»

**Аргументы**

* *ColumnName*: имя опциона для присвоения сингулярной колонке вывода.
* *ScalarExpression*: Скалар выражение для оценки.

**Возвращает**

Одноколонка, однорядная таблица, одна ячейка которой имеет значение оцененного *ScalarExpression.*

**Примеры**

Оператор `print` полезен как быстрый способ оценить одно или несколько скалярных выражений и сделать таблицу с одним рядом из полученных значений.
Пример:

```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```

```kusto
print banner=strcat("Hello", ", ", "World!")
```