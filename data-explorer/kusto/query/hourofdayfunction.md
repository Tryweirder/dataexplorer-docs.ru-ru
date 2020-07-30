---
title: хаурофдай () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается хаурофдай () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a001a2f9faa1eb7ea3636ee6fbfde3cb0489158
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347516"
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

## <a name="returns"></a>Возвращаемое значение

`hour number`день (0-23).