---
title: Регулярные выражения - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны регулярные выражения в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0bc5dc6705d6cada446716f2f9d84618322ecd76
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510529"
---
# <a name="regular-expressions"></a>Регулярные выражения

Регулярный синтаксис выражения RE2 описывает синтаксис регулярной библиотеки выражений, используемой Kusto (re2).
Есть несколько функций в Kusto выполнять строки соответствия, выбор и извлечение с помощью регулярного выражения

- [countof()](countoffunction.md)
- [экстракт()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [соответствует regex](datatypes-string-operators.md)
- [Оператор parse](parseoperator.md)
- [заменить()](replacefunction.md)
- [отделка()](trimfunction.md)
- [трименд()](trimendfunction.md)
- [тримстарт()](trimstartfunction.md)

Регулярный синтаксис выражения, поддерживаемый Kusto, является библиотекой [re2](https://github.com/google/re2/wiki/Syntax)и подробно описан ниже. Обратите внимание, что эти выражения должны быть `string` закодированы в Кусто как буквальные, и все правила цитирования строки Кусто применяются. Например, регулярное `\A` выражение соответствует началу строки (см. таблицу ниже), и `"\\A"` указывается в Kusto`\`как строка буквального (обратите внимание на "лишний" backslash () характер.)

