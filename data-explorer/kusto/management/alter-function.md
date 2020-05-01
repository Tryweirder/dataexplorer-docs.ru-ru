---
title: . ALTER FUNCTION — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается. ALTER FUNCTION в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d8248fdd9428df11a8e77316eec621102ddff9d6
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617805"
---
# <a name="alter-function"></a>.alter function

Изменяет существующую функцию и сохраняет ее в метаданных базы данных.
Правила для типов параметров и инструкций CSL совпадают с правилами для [ `let` операторов](../query/letstatement.md).

**Синтаксис**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|Выходной параметр |Тип |Описание
|---|---|--- 
|Имя  |Строка |Имя функции.
|Параметры  |Строка |Параметры, необходимые для функции.
|Текст  |Строка |(Ноль или более) `let` инструкции, за которыми следует допустимое выражение CSL, которое вычисляется при вызове функции.
|Папка|Строка|Папка, используемая для классификации функций пользовательского интерфейса. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей пользовательского интерфейса.

> [!NOTE]
> * Если функция не существует, возвращается ошибка. Сведения о создании новой функции см. в описании [функции. Create.](create-function.md)
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший функцию, может изменять функцию. 
> * В `let` инструкциях поддерживаются не все типы Kusto. Поддерживаются следующие типы: String, Long, DateTime, TimeSpan и Double.
> * Используйте `skipvalidation` , чтобы пропустить семантическую проверку функции. Это полезно, когда функции создаются в неправильном порядке, а клавиша F1, которая использует F2, создается ранее.
 
**Пример** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|Имя |Параметры |Текст|Папка|DocString
|---|---|---|---|---
|MyFunction2 |(Милимит: long)| {Стормевентс &#124; ограничение Милимит}|MyFolder|Демонстрационная функция с параметром|
