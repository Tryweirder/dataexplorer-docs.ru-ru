---
title: countof() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описано количество () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d932fbcea9b38849e7d7de09230c9a5aa9fa8e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516904"
---
# <a name="countof"></a>countof()

Подсчитывает вхождения подстроки в строку. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

```kusto
countof("The cat sat on the mat", "at") == 3
countof("The cat sat on the mat", @"\b.at\b", "regex") == 3
```

**Синтаксис**

`countof(`*текстовый* `,` `,` *поиск* и *вид*`)`

**Аргументы**

* *текст*: Строка.
* *поиск*: Обычная строка или [регулярное выражение,](./re2.md) чтобы соответствовать внутри *текста.*
* *вид* `"normal"|"regex"` : `normal`По умолчанию . 

**Возвращает**

Количество совпадений для строки поиска в контейнере. Совпадения для обычных строк могут перекрывать друг друга, для регулярных выражений — не могут.

**Примеры**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (не 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    