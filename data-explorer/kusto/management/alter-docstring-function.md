---
title: . ALTER FUNCTION docstring — Azure обозреватель данных
description: В этой статье описывается `.alter function docstring` Обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cad374c767b126d60b7c701f596bddf3c20c4345
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763884"
---
# <a name="alter-function-docstring"></a>. ALTER FUNCTION docstring

Изменяет `DocString` значение существующей функции.

`.alter``function` *FunctionName* `docstring` *Документация по* FunctionName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший функцию, может изменять функцию.
> * Если функция не существует, возвращается ошибка. Дополнительные сведения о создании новой функции см. в описании [функции. Create](create-function.md).

|Выходной параметр |Тип |Описание
|---|---|--- 
|name  |Строка |Имя функции
|Параметры  |Строка |Параметры, необходимые для функции
|Текст  |Строка |(Ноль или более) `let` инструкции, за которыми следует допустимое выражение CSL, вычисляемое при вызове функции
|Папка|Строка|Папка, используемая для классификации функций пользовательского интерфейса. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей пользовательского интерфейса

**Пример** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|name |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(Милимит: long)| {Стормевентс &#124; ограничение Милимит}|MyFolder|Обновленный docstring|
