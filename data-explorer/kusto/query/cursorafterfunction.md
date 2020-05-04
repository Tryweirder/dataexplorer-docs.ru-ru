---
title: cursor_after () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается cursor_after () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 9fab1ec936e950368667fc3afb133dcd952e44b5
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737697"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

Предикат для записей таблицы для сравнения времени приема с курсором базы данных.

**Синтаксис**

`cursor_after``(` *RHS*`)`

**Аргументы**

* *RHS*: пустой строковый литерал или допустимое значение курсора базы данных.

**Возвращает**

Скалярное значение типа `bool` , указывающее, была ли запись принята после курсора базы данных *RHS* (`true`) или not (`false`).

**Примечания**

Дополнительные сведения о курсорах базы данных см. в разделе [курсоры базы данных](../management/databasecursor.md) .

Эта функция может вызываться только для записей таблицы, для которых включена [Политика инжестионтиме](../management/ingestiontimepolicy.md) .

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
