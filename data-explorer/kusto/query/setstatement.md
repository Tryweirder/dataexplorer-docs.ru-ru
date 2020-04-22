---
title: Заяц - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описано заявление Set в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8cb9c1d72f1b2e8e4bfbbd28d67c04295c9ccf5b
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765570"
---
# <a name="set-statement"></a>Инструкция set

::: zone pivot="azuredataexplorer"

Заявление `set` используется для установки опции запроса на время запроса.
Параметры запроса управляют выполнением запроса и возвращением результатов. Они могут быть флагами Boolean (выключается по умолчанию), или иметь величину значения. Запрос может содержать один или несколько операторов set либо не содержать ни одного. Набор инструкций влияет только на табулярные операторы выражения, которые отсеивают их в порядке программы.

* Параметры запроса также могут быть включены программно, установив их в объекте. `ClientRequestProperties` Смотрите [здесь](../api/netfx/request-properties.md).
  
* Параметры запроса формально не являются частью языка Kusto и могут быть изменены без рассмотрения как нарушение изменения языка.

**Синтаксис**

`set`*OptionName* `=` - *OptionValue*

**Пример**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
