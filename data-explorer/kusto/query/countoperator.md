---
title: Оператор Count — Azure обозреватель данных
description: В этой статье описывается оператор Count в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: 969efc142f1cd823b319a5c98494542fb2603f24
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348740"
---
# <a name="count-operator"></a>Оператор count

Возвращает количество записей во входном наборе записей.

## <a name="syntax"></a>Синтаксис

`T | count`

## <a name="arguments"></a>Аргументы

*T*— табличные данные, записи которых будут подсчитаны.

## <a name="returns"></a>Результаты

Эта функция возвращает таблицу с одной записью и столбцом типа `long`. Значение единственной ячейки — количество записей в *T*. 

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
