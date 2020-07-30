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
ms.openlocfilehash: 879fe4aac2a6714f3d7ab16a63c69cd8215bbb04
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348604"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor();

::: zone pivot="azuredataexplorer"

Извлекает текущее значение курсора базы данных в области. (Имена `cursor_current` и `current_cursor` являются синонимами.)

## <a name="syntax"></a>Синтаксис

`cursor_current()`

## <a name="returns"></a>Результаты

Возвращает одиночное значение типа `string` , которое кодирует текущее значение курсора базы данных в области.

**Примечания**

Дополнительные сведения о курсорах базы данных см. в разделе [курсоры базы данных](../management/databasecursor.md) .

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
