---
title: cursor_after () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается cursor_after () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 39ec32322b74b55182522e4bbb04aa0c3830d8d2
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766027"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

Предикат над записями таблицы для сравнения времени приема с курсором базы данных.

**Синтаксис**

`cursor_after``(` *RHS*`)`

**Аргументы**

* *RHS*: Либо пустая строка буквального, или допустимое значение курсора базы данных.

**Возвращает**

Масштабируемое значение `bool` типа, которое указывает, была ли запись попадает в`true`систему`false`после курсора базы данных *RHS* () или нет ( ).

**Примечания**

Дополнительные сведения о курсорах баз данных можно узнать о [курсорах баз данных.](../management/databasecursor.md)

Эта функция может быть вызвана только на записи таблицы, в которой [включена политика IngestionTime.](../management/ingestiontimepolicy.md)

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
