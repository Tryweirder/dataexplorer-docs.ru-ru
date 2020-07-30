---
title: Now () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Now () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9beae6edd1361715dfe84f851ca0a9bb69f4299c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346581"
---
# <a name="now"></a>now()

Возвращает текущее время в формате UTC, при необходимости смещается на заданный интервал времени.
Эта функция может использоваться в выражении несколько раз, и значение времени будет одинаковым для всех случаев.

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>Синтаксис

`now(`[*offset*]`)`

## <a name="arguments"></a>Аргументы

* *offset*: а `timespan` , добавленное к текущему времени UTC. По умолчанию: 0.

## <a name="returns"></a>Результаты

Текущее время UTC в формате `datetime`.

`now()` + *собой* 

## <a name="example"></a>Пример

Определяет интервал с момента события, определенного предикатом.

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```