---
title: Регулярные выражения в Azure Data Explorer | Документация Майкрософт
description: В этой статье описаны регулярные выражения в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513086"
---
# <a name="re2-syntax"></a>Синтаксис RE2

Синтаксис регулярных выражений RE2 отражает синтаксис библиотеки регулярных выражений, используемой Kusto (RE2).
В Kusto есть несколько функций, которые выполняют сопоставление, выбор и извлечение строк с помощью регулярного выражения:

- [countof()](countoffunction.md)
- [extract()](extractfunction.md);
- [extract_all()](extractallfunction.md)
- [соответствует regex](datatypes-string-operators.md)
- [Оператор parse](parseoperator.md)
- [replace()](replacefunction.md);
- [trim()](trimfunction.md)
- [trimend()](trimendfunction.md);
- [trimstart()](trimstartfunction.md).

Синтаксис регулярных выражений, поддерживаемый Kusto, тот же, что и в [библиотеке RE2](https://github.com/google/re2/wiki/Syntax). Эти выражения нужно закодировать в Kusto как литералы `string`. При этом применяются все правила Kusto, касающиеся заключения в кавычки. Например, регулярное выражение `\A` соответствует началу строки и указывается в Kusto как строковый литерал `"\\A"` (обратите внимание на "дополнительный" знак обратной косой черты (`\`)).
