---
title: Функция .alter - Исследователь данных Azure Документы Майкрософт
description: В этой статье описана функция .alter в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 3fb7b3aab9d140d5f3660ef1384bf54efa9cd825
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522463"
---
# <a name="alter-function"></a>функция .alter

Изменяет существующую функцию и хранит ее в метаданных базы данных.
Правила для типов параметров и операторы CSL такие же, как и для [ `let` инструкций.](../query/letstatement.md)

**Синтаксис**

```
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Имя функции.
|Параметры  |Строка |Параметры, требуемые функцией.
|Текст  |Строка |(Ноль или больше) `let` за которым следует действительное выражение CSL, которое оценивается по вызову функции.
|Папка|Строка|Папка, используемая для категоризации функций uI. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей uI.

> [!NOTE]
> * Если функция не существует, возвращается ошибка. Для создания новой функции [см.](create-function.md)
> * Требуется [разрешение администрирования администрирования базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных,](../management/access-control/role-based-authorization.md) который первоначально создал функцию, может изменить функцию. 
> * Не все типы Kusto поддерживаются в заявлениях. `let` Поддерживаемые типы: строка, длина, время даты, временной промежуток и двойной.
> * Используйте, `skipvalidation` чтобы пропустить семантическую проверку функции. Это полезно, когда функции создаются в неправильном порядке, а F1, использующее F2, создается ранее.
 
**Пример** 

```
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: длинный)| «StormEvents &#124; ограничить myLimit»|MyFolder|Функция демо с параметром|