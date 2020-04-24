---
title: docstring функции .alter - Azure Data Explorer Документы Майкрософт
description: В этой статье описана функция docstring .alter в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25f6dac67f65add545f7215b44daafb0257a8375
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522582"
---
# <a name="alter-function-docstring"></a>docstring функции .alter

Изменяет значение DocString существующей функции.

`.alter``function` *Документация FunctionName* `docstring` *Documentation*

> [!NOTE]
> * Требуется [разрешение администрирования администрирования базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных,](../management/access-control/role-based-authorization.md) который первоначально создал функцию, может изменить функцию. 
> * Если функция не существует, возвращается ошибка. Для создания новой функции [см.](create-function.md)

|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Имя функции. 
|Параметры  |Строка |Параметры, требуемые функцией.
|Текст  |Строка |(Ноль или больше) `let` за которым следует действительное выражение CSL, которое оценивается по вызову функции.
|Папка|Строка|Папка, используемая для категоризации функций uI. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей uI.

**Пример** 

```
.alter function MyFunction1 docstring "Updated docstring"
```
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: длинный)| «StormEvents &#124; ограничить myLimit»|MyFolder|Обновленный документ|