---
title: каунтоф () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается каунтоф () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5204aff94f2fd6e6c824f66bdc30000b46c05501
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249237"
---
# <a name="countof"></a>countof()

Подсчитывает вхождения подстроки в строку. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

## <a name="syntax"></a>Синтаксис

`countof(`*текстовая надпись* `,` *Search* [ `,` *тип*]`)`

## <a name="arguments"></a>Аргументы

* *Text*: строка.
* *Поиск*: обычная строка или [регулярное выражение](./re2.md) для сопоставления внутри *текста*.
* *тип*: `"normal"|"regex"` по умолчанию `normal` . 

## <a name="returns"></a>Результаты

Количество совпадений для строки поиска в контейнере. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

## <a name="examples"></a>Примеры

|Вызов функции|Результат|
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (не 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    