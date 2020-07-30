---
title: Оператор аспекта в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор аспекта в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0e5bc062b99a97b8d11c11312aac2d5829d6584b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348026"
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