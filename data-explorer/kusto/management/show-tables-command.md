---
title: таблицы .show - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны таблицы .show в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a8faf307a241d1ba0f73436d9503a56c9078e471
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519641"
---
# <a name="show-tables"></a>.показать таблицы

Возвращает набор, содержащий указанную таблицу или все таблицы в базе данных.

Требуется [разрешение просмотра базы данных.](../management/access-control/role-based-authorization.md)

```
.show tables
.show tables (T1, ..., Tn)
```

**Вывод**

|Выходной параметр |Тип |Описание
|---|---|---
|TableName  |Строка |Имя таблицы.
|имя_базы_данных  |Строка |База данных, к которой принадлежит таблица.
|Папка |Строка |Папка стола.
|DocString |Строка |Строка, документирующая таблицу.

**Пример выходных данных**

|Имя таблицы |Имя базы данных |Папка | DocString
|---|---|---|---
|Table1 |БД1 |Журналы |Содержит журналы служб
|Таблица_2 |БД1 | Отчеты |
|Table3 |БД1 | | Расширенная информация |
|Таблица4 |DB2 | Метрики| Содержит информацию об производительности служб