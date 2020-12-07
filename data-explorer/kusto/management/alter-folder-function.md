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
ms.openlocfilehash: 37cedb7ba6e58f5b434101cd23b876cf18c2b78c
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321732"
---
# <a name="alter-function-folder"></a>.alter function folder

Изменяет значение папки для существующей функции.

`.alter``function` *FunctionName* `folder` *Папка* FunctionName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший функцию, может изменять функцию. 
> * Если функция не существует, возвращается ошибка. Для создания новой функции [`.create function`](create-function.md)

|Выходной параметр |Тип |Описание
|---|---|--- 
|name  |Строка |Имя функции. 
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