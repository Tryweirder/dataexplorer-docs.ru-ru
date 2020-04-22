---
title: cursor_before_or_at () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается cursor_before_or_at () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4d1752c69a6f3515b94c4050cef8f518ff58a235
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765970"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

Предикат над записями таблицы для сравнения времени приема с курсором базы данных.

**Синтаксис**

`cursor_before_or_at``(` *RHS*`)`

**Аргументы**

* *RHS*: Либо пустая строка буквального, или допустимое значение курсора базы данных.

**Возвращает**

Масштабируемое значение `bool` типа, которое указывает, была ли запись попадает в систему до`false`или в курсоре базы данных *RHS* ()`true`или нет ( ).

**Примечания**

Дополнительные сведения о курсорах баз данных можно узнать о [курсорах баз данных.](../management/databasecursor.md)

Эта функция может быть вызвана только на записи таблицы, в которой [включена политика IngestionTime.](../management/ingestiontimepolicy.md)

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
