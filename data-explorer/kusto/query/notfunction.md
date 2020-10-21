---
title: not () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается not () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3224c48b963c051d0d65d27a2e64ec8317be30c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252187"
---
# <a name="not"></a>not()

Изменяет значение аргумента на противоположное `bool` .

```kusto
not(false) == true
```

## <a name="syntax"></a>Синтаксис

`not(`*выражения*`)`

## <a name="arguments"></a>Аргументы

* *expr*: `bool` выражение, которое должно быть отменено.

## <a name="returns"></a>Результаты

Возвращает Инвертированное логическое значение `bool` аргумента.