---
title: тогуид () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается тогуид () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d122833f4797c8503dd41cc8ba861554d6924338
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250388"
---
# <a name="toguid"></a>toguid()

Преобразует входные данные в [`guid`](./scalar-data-types/guid.md) представление.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> Предпочитать использовать [GUID ()](./scalar-data-types/guid.md) , если это возможно.

## <a name="syntax"></a>Синтаксис

`toguid(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в [`guid`](./scalar-data-types/guid.md) скалярное значение. 

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результат будет [`guid`](./scalar-data-types/guid.md) скалярным.
Если преобразование не выполнено, результатом будет `null` .
