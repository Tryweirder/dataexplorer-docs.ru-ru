---
title: cursor_before_or_at () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается cursor_before_or_at () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 94644ae8bfae934bc908c7e06903be3825d8ccd8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348621"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

Предикат для записей таблицы для сравнения времени приема с курсором базы данных.

## <a name="syntax"></a>Синтаксис

`cursor_before_or_at``(` *RHS*`)`

## <a name="arguments"></a>Аргументы

* *RHS*: пустой строковый литерал или допустимое значение курсора базы данных.

## <a name="returns"></a>Результаты

Скалярное значение типа `bool` , указывающее, была ли запись принята до или в курсоре базы данных *RHS* ( `true` ) или not ( `false` ).

**Примечания**

Дополнительные сведения о курсорах базы данных см. в разделе [курсоры базы данных](../management/databasecursor.md) .

Эта функция может вызываться только для записей таблицы, для которых включена [Политика инжестионтиме](../management/ingestiontimepolicy.md) .

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
