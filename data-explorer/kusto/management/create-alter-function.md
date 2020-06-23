---
title: . Create-или-ALTER FUNCTION — Azure обозреватель данных
description: В этой статье описывается, как создать или изменить функцию в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f19ca38f344f10b9dd8e4491b467eaad5ca022bc
ms.sourcegitcommit: a034b6a795ed5e62865fcf9340906f91945b3971
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85197248"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

Создает хранимую функцию или изменяет существующую функцию и сохраняет ее в метаданных базы данных.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

Если функция с указанным *FunctionName* не существует в метаданных базы данных, команда создает новую функцию. В противном случае эта функция будет изменена.

**Пример**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|Имя|Параметры|Текст|Папка|DocString|
|---|---|---|---|---|
|тестфунктион|(Милимит: int)|{Стормевентс &#124; take Милимит}|MyFolder|Демонстрационная функция с параметром|
