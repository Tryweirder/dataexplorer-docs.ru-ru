---
title: утверждение () - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается утверждение () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f01df7bc85171eefabeb2ed835109f0faef81e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518417"
---
# <a name="assert"></a>assert()

Проверяет условие. Если условие является ложным, выводит сообщения об ошибках и не выполняется запрос.

**Синтаксис**

`assert(`*condition*`, `*сообщение* состояния`)`

**Аргументы**

* *состояние*: Условное выражение для оценки. При наличии `false`условия указанное сообщение используется для сообщения об ошибке. Если `true`условие, оно `true` возвращается в результате оценки. Состояние должно быть оценено в постоянное течение этапа анализа запроса.
* *сообщение*: Сообщение используется, если `false`утверждение оценивается как . *Сообщение* должно быть строкой буквально.


**Возвращает**

* `true`- если условие`true`
* Вызывает семантическую ошибку, если `false`состояние оценивается как.

**Примечания**

* `condition`должны быть оценены в постоянную на этапе анализа запроса. Другими словами, он может быть построен из других выражений, отсылающих к константам, и не может быть связан с рядом-контекстом.

**Примеры**

Следующий запрос определяет функцию, `checkLength()` которая проверяет длину `assert` строки ввода и использует для проверки параметра длины ввода (проверяет, что он больше нуля).

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and 
    strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=long(-1), input)
```

Запуск этого запроса дает ошибку:  
`assert() has failed with message: 'Length must be greater than zero'`


Пример работы `len` с допустимыми входными путами:

```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=3, input)
```

|input|
|---|
|4567|