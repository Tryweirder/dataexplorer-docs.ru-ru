---
title: тогуид () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается тогуид () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 689ee6bf7b3fcb27dced20b06a9002659622902e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804140"
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

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результат будет [`guid`](./scalar-data-types/guid.md) скалярным.
Если преобразование не выполнено, результатом будет `null` .
