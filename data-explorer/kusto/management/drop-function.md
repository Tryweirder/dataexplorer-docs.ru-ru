---
title: Функция .drop - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описана функция .drop в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8930f7333ff18fad0d5b3dbbebe9328f8bf7a0b9
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744787"
---
# <a name="drop-function"></a>.drop function

Выпадает функция из базы данных.
Для удаления нескольких функций из базы данных [см.](#drop-functions)

**Синтаксис**

`.drop``function` *ФункцияИмя* `ifexists`

* `ifexists`: Если указано, изменяет поведение команды, чтобы не потерпеть неудачу для несуществующей функции.

> [!NOTE]
> * Требуется [разрешение админа базы данных.](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных,](../management/access-control/role-based-authorization.md) который первоначально создал функцию, может изменить функцию.  
    
|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Название функции, которая была удалена
 
**Пример** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>функции .drop

Выпадает несколько функций из базы данных.

**Синтаксис**

`.drop``functions` *(FunctionName1*, *FunctionName2*,..) (если не существующие)

**Возвращает**

Эта команда возвращает список оставшихся функций в базе данных.

|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Имя функции. 
|Параметры  |Строка |Параметры, требуемые функцией.
|Текст  |Строка |(Ноль или больше) `let` за которым следует действительное выражение CSL, которое оценивается по вызову функции.
|Папка|Строка|Папка, используемая для категоризации функций uI. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей uI.

Требуется [разрешение на работу админа функции.](../management/access-control/role-based-authorization.md)

**Пример** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
