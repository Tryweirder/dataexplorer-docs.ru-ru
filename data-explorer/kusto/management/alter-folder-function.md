---
title: Папка функции .alter - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается папка функции .alter в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 140991c723ebdd12fa17000ea845adbbfdd27771
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522548"
---
# <a name="alter-function-folder"></a>папка функции .alter

Изменяет значение Folder существующей функции.

`.alter``function` *ФункцияИмя* `folder` *Фолдер*

> [!NOTE]
> * Требуется [разрешение администрирования администрирования базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных,](../management/access-control/role-based-authorization.md) который первоначально создал функцию, может изменить функцию. 
> * Если функция не существует, возвращается ошибка. Для создания новой [функции функция .create](create-function.md)

|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Имя функции. 
|Параметры  |Строка |Параметры, необходимые функции.
|Текст  |Строка |(Ноль или больше) Пусть заявления следуют действительным выражением CSL, которое оценивается по вызову функции.
|Папка|Строка|Папка, используемая для категоризации функций uI. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей uI.

**Пример** 

```
.alter function MyFunction1 folder "Updated Folder"
```
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: длинный)| «StormEvents &#124; ограничить myLimit»|Обновленный Фолдер|Некоторые DocString|

```
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: длинный)| «StormEvents &#124; ограничить myLimit»|Первый уровень/второй уровень|Некоторые DocString|