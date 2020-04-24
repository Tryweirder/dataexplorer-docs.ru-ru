---
title: tostring () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны tostring() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51aaf90b60653a648457dc00200168aec7fbefd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505888"
---
# <a name="tostring"></a>tostring()

Преобразует входные данные в представление строки.

```kusto
tostring(123) == "123"
```

**Синтаксис**

`tostring(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в строку. 

**Возвращает**

Если значение *Expr* является ненулевым результатом, будет строка представления *Expr*.
Если значение *Expr* является нулевым, результат будет пустой строкой.
 