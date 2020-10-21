---
title: cursor_current (), current_cursor () — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается cursor_current (), current_cursor () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1b2e6f868d5117cbdd3db6ba88c6b77e5f0e8ccf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252438"
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
