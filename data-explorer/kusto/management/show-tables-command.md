---
title: . Отображение таблиц в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как отобразить таблицы в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3da4f705d3182c52d06c7767a12d9be15a219e5c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618332"
---
# <a name="show-tables"></a>. Отображение таблиц

Возвращает набор, содержащий указанную таблицу или все таблицы в базе данных.

Требуется [разрешение на просмотр базы данных](../management/access-control/role-based-authorization.md).

```kusto
.show tables
.show tables (T1, ..., Tn)
```

**Выходные данные**

|Выходной параметр |Тип |Описание
|---|---|---
|TableName  |Строка |Имя таблицы.
|имя_базы_данных  |Строка |База данных, к которой принадлежит таблица.
|Папка |Строка |Папка таблицы.
|DocString |Строка |Строка, задокументированная в таблице.

**Пример выходных данных**

|Имя таблицы |Имя базы данных |Папка | DocString
|---|---|---|---
|Table1 |БД1 |Журналы |Содержит журналы служб
|Таблица_2 |БД1 | Отчеты |
|Table3 |БД1 | | Расширенные сведения |
|Table4 |DB2 | Метрики| Содержит сведения о производительности служб
