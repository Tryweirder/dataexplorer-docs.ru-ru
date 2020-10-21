---
title: Assert () — Azure обозреватель данных
description: В этой статье описывается Assert () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 442fbec2742a4d1edc7a9ad03a81db27e6d18574
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252775"
---
# <a name="assert"></a>assert()

Проверяет условие. Если условие имеет значение false, выводит сообщения об ошибках и завершает выполнение запроса.

## <a name="syntax"></a>Синтаксис

`assert(`*условие* `, ` *сообщение об ошибке*`)`

## <a name="arguments"></a>Аргументы

* *условие*: проверяемое условное выражение. Если условие имеет значение `false` , то указанное сообщение используется для сообщения об ошибке. Если условие имеет значение `true` , оно возвращает `true` результат вычисления. Во время фазы анализа запроса условие должно быть вычислено как константное.
* *Message*: сообщение, используемое при вычислении утверждения `false` . *Сообщение* должно быть строковым литералом.

> [!NOTE]
> `condition` необходимо вычислить до константы на этапе анализа запроса. Иными словами, он может быть создан из других выражений, ссылающихся на константы, и не может быть привязан к контексту строки.

## <a name="returns"></a>Результаты

* `true` — Если условие `true`
* Вызывает семантическую ошибку, если условие оценивается как `false` .

## <a name="examples"></a>Примеры

Следующий запрос определяет функцию `checkLength()` , которая проверяет длину входной строки и использует `assert` для проверки параметра длины входных данных (проверяет, что он больше нуля).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

Выполнение этого запроса приведет к ошибке:  
`assert() has failed with message: 'Length must be greater than zero'`


Пример запуска с допустимыми `len` входными данными:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
