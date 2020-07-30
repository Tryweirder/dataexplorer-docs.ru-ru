---
title: Инструкция SET — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается инструкция SET в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: fbb5d3765b4be20b55cd7e3fa155a26e429c61e8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351154"
---
# <a name="set-statement"></a>Инструкция set

::: zone pivot="azuredataexplorer"

`set`Оператор используется для задания параметра запроса на время выполнения запроса.
Параметры запроса управляют выполнением запроса и возвращением результатов. Они могут быть логическими флагами (отключены по умолчанию) или иметь целочисленное значение. Запрос может содержать один или несколько операторов set либо не содержать ни одного. Инструкции SET влияют только на операторы табличных выражений, которые были в конце их порядка в программе.

* Параметры запроса можно также включить программно, задав их в `ClientRequestProperties` объекте. См. [здесь](../api/netfx/request-properties.md).
  
* Параметры запроса не формально являются частью языка Kusto и могут быть изменены без необходимости рассматриваться как коренное изменение языка.

## <a name="syntax"></a>Синтаксис

`set`*OptionName* [ `=` *OptionValue*]

## <a name="example"></a>Пример

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
