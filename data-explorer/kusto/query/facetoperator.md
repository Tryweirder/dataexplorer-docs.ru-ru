---
title: Оператор аспекта в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор аспекта в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2398652e7cad7456d294f11353cfdfe049080503
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249062"
---
# <a name="facet-operator"></a>Оператор facet

Возвращает набор таблиц, по одному для каждого указанного столбца.
Каждая таблица указывает список значений, сделанных по столбцу.
Можно создать дополнительную таблицу с помощью `with` предложения.

## <a name="syntax"></a>Синтаксис

*T* `| facet by` *ColumnName* [ `, ` ...] [ `with (` *филтерпипе*`)`

## <a name="arguments"></a>Аргументы

* *ColumnName:* Имя столбца во входных данных, которое будет суммироваться как выходная таблица.
* *филтерпипе:* Выражение запроса, применяемое к входной таблице для получения одного из выходов.

## <a name="returns"></a>Результаты

Несколько таблиц: одна для `with` предложения и по одной для каждого столбца.

## <a name="example"></a>Пример

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```