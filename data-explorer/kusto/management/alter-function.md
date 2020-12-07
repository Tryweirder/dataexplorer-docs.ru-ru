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
ms.openlocfilehash: fc7c29542f63ea9b659bd3318d1442d9bca4ae48
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321715"
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
|name  |Строка |Имя функции.
|Параметры  |Строка |Параметры, необходимые для функции.
|Текст  |Строка |(Ноль или более) `let` инструкции, за которыми следует допустимое выражение CSL, которое вычисляется при вызове функции.
|Папка|Строка|Папка, используемая для классификации функций пользовательского интерфейса. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей пользовательского интерфейса.

> [!NOTE]
> * Если функция не существует, возвращается ошибка. Сведения о создании новой функции см. в разделе. [`.create function`](create-function.md)
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший функцию, может изменять функцию. 
> * В инструкциях поддерживаются не все типы Kusto `let` . Поддерживаются следующие типы: String, Long, DateTime, TimeSpan и Double.
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
