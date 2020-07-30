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
ms.openlocfilehash: a2eec6a582c4c8fc6cda6b3cf9a304f41ab48143
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350742"
---
# <a name="toguid"></a>toguid()

Преобразует входные данные в [`guid`](./scalar-data-types/guid.md) представление.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

## <a name="syntax"></a>Синтаксис

`toguid(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в [`guid`](./scalar-data-types/guid.md) скалярное значение. 

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результат будет [`guid`](./scalar-data-types/guid.md) скалярным.
Если преобразование не выполнено, результатом будет `null` .

*Примечание*. предпочитать использование [GUID ()](./scalar-data-types/guid.md) , если это возможно.