---
title: монсофеар () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается монсофеар () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af015146bb2f07d83d4333312a96d5b80c67190a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346734"
---
# <a name="monthofyear"></a>monthofyear()

Возвращает целое число, представляющее номер месяца данного года.

Другой псевдоним:-month ()

```kusto
monthofyear(datetime("2015-12-14"))
```

## <a name="syntax"></a>Синтаксис

`monthofyear(`*a_date*`)`

## <a name="arguments"></a>Аргументы

* `a_date` — значение в формате `datetime`.

## <a name="returns"></a>Результаты

`month number`указанного года.