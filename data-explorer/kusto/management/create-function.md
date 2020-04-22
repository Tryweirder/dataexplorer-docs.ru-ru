---
title: функция .create - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается функция .create в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc2caa5dcc886964b42bf1915bf3a003f2d32c7a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744457"
---
# <a name="create-function"></a>.create function

Создает функцию сохраненного, которая является многоразовой [ `let` функцией оператора](../query/letstatement.md) с заданное имя. Определение функции сохраняется с метаданными базы данных.

Функции могут вызывать другие функции (рекурсивность не поддерживается), а `let` операторы разрешены как часть органа *функции.* Смотрите [ `let` заявления](../query/letstatement.md).

Правила для типов параметров и операторы CSL такие же, как и для [ `let` инструкций.](../query/letstatement.md)
    
**Синтаксис**

`.create``function` `ifnotexists` `)` `(` `:` `,` *Documentation* `)` *FunctionName* *ParamType* *FolderName* `=` Документация`,` - `folder` `skipvalidation` FolderName - "правда" - ФункцияName *ParamName ParamType* . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .`,` `=` `with` `(``docstring` `=` `)` `{` *ФункцияТело*`}`

**Вывод**    
    
|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Имя функции. 
|Параметры  |Строка |Параметры, требуемые функцией.
|Текст  |Строка |(Ноль или больше) `let` за которым следует действительное выражение CSL, которое оценивается по вызову функции.
|Папка|Строка|Папка, используемая для категоризации функций uI. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей uI.

> [!NOTE]
> * Если функция уже существует:
>    * Если `ifnotexists` флаг указан, команда игнорируется (изменение не применяется).
>    * Если `ifnotexists` флаг не указан, ошибка возвращается.
>    * Для изменения существующей функции [см.](alter-function.md)
> * Требуется [разрешение пользователя базы данных.](../management/access-control/role-based-authorization.md)
> * Не все типы `let` данных поддерживаются в заявлениях. Поддерживаемые типы: boolean, строка, длинные, дата, временной промежуток, двойной и динамичный.
> * Используйте 'skipvalidation', чтобы пропустить семантическую проверку функции. Это полезно, когда функции создаются в неправильном порядке, а F1, использующее F2, создается ранее.

**Примеры** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|Имя|Параметры|Текст|Папка|DocString|
|---|---|---|---|---|
|MyFunction1|()|«StormEvents &#124; лимит 100»|Демонстрация|Простая функция демо|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|Имя|Параметры|Текст|Папка|DocString|
|---|---|---|---|---|
|MyFunction2|(myLimit:long)|«StormEvents &#124; ограничить myLimit»|Демонстрация|Функция демо с параметром|
