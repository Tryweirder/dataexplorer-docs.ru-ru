---
title: cursor_current (), current_cursor () — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается cursor_current (), current_cursor () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5291fa664dc4736179d7f20984eacfd44efd5888
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737663"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor();

::: zone pivot="azuredataexplorer"

Извлекает текущее значение курсора базы данных в области. (Имена `cursor_current` и `current_cursor` являются синонимами.)

**Синтаксис**

`cursor_current()`

**Возвращает**

Возвращает одиночное значение типа `string` , которое кодирует текущее значение курсора базы данных в области.

**Примечания**

Дополнительные сведения о курсорах базы данных см. в разделе [курсоры базы данных](../management/databasecursor.md) .

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
