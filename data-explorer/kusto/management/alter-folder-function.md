---
title: . ALTER FUNCTION папка-Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается. изменение папки функции в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 6becb5e29fd5771e1027c824b5a3539ed3c33b88
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617839"
---
# <a name="alter-function-folder"></a>. изменение папки функции

Изменяет значение папки для существующей функции.

`.alter``function` *FunctionName* `folder` *Папка* FunctionName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший функцию, может изменять функцию. 
> * Если функция не существует, возвращается ошибка. Создание новой функции, [. Create, функция](create-function.md)

|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Имя функции. 
|Параметры  |Строка |Параметры, необходимые для функции.
|Текст  |Строка |(Ноль или более) Инструкции Let, за которыми следует допустимое выражение CSL, которое вычисляется при вызове функции.
|Папка|Строка|Папка, используемая для классификации функций пользовательского интерфейса. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей пользовательского интерфейса.

**Пример** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(Милимит: long)| {Стормевентс &#124; ограничение Милимит}|Обновленная папка|Некоторые DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(Милимит: long)| {Стормевентс &#124; ограничение Милимит}|Первый уровень Левел\секонд|Некоторые DocString|