---
title: . ALTER FUNCTION docstring — Azure обозреватель данных
description: В этой статье описывается. ALTER FUNCTION docstring в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: e364a3cc5607b1a4b629954c93bf90e9173c00f1
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321766"
---
# <a name="alter-function-docstring"></a>.alter function docstring

Изменяет `DocString` значение существующей функции.

`.alter``function` *FunctionName* `docstring` *Документация по* FunctionName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший функцию, может изменять функцию.
> * Если функция не существует, возвращается ошибка. Дополнительные сведения о создании новой функции см. в разделе [`.create function`](create-function.md) .

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
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(Милимит: long)| {Стормевентс &#124; ограничение Милимит}|MyFolder|Обновленный docstring|
