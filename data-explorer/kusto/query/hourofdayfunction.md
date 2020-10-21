---
title: хаурофдай () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается хаурофдай () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d83725e067f12d05382a905aee935f9433ef7a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244733"
---
# <a name="hourofday"></a>hourofday()

Возвращает целое число, представляющее номер часа заданной даты

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

## <a name="syntax"></a>Синтаксис

`hourofday(`*a_date*`)`

## <a name="arguments"></a>Аргументы

* `a_date` — значение в формате `datetime`.

## <a name="returns"></a>Результаты

`hour number` день (0-23).