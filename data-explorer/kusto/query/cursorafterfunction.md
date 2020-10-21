---
title: cursor_after () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается cursor_after () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3b1a89ab84b62241058a24573c0362f7215f82c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252469"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

Предикат для записей таблицы для сравнения времени приема с курсором базы данных.

## <a name="syntax"></a>Синтаксис

`cursor_after``(` *RHS*`)`

## <a name="arguments"></a>Аргументы

* *RHS*: пустой строковый литерал или допустимое значение курсора базы данных.

## <a name="returns"></a>Результаты

Скалярное значение типа `bool` , указывающее, была ли запись принята после курсора базы данных *RHS* ( `true` ) или not ( `false` ).

**Примечания**

Дополнительные сведения о курсорах базы данных см. в разделе [курсоры базы данных](../management/databasecursor.md) .

Эта функция может вызываться только для записей таблицы, для которых включена [Политика инжестионтиме](../management/ingestiontimepolicy.md) .

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
