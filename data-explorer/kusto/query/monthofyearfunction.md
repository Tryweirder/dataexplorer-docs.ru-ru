---
title: монсофеар () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается монсофеар () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd40612236b9c9b249c9c070bc784c518be0faae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250990"
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

`month number` указанного года.