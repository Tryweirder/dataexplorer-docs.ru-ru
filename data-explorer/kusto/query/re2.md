---
title: Регулярные выражения в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются регулярные выражения в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 6bdf666b46adea8105b61fc2b907fc060530ba96
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967593"
---
# <a name="re2-syntax"></a>Синтаксис RE2

Синтаксис регулярных выражений RE2 описывает синтаксис библиотеки регулярных выражений, используемой Kusto (RE2).
Существует несколько функций в Kusto, которые выполняют сопоставление строк, выделение и извлечение с помощью регулярного выражения.

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [соответствует regex](datatypes-string-operators.md)
- [Оператор parse](parseoperator.md)
- [replace()](replacefunction.md)
- [trim()](trimfunction.md)
- [TrimEnd ()](trimendfunction.md)
- [TrimStart ()](trimstartfunction.md)

Синтаксис регулярных выражений, поддерживаемый Kusto, состоит из [библиотеки RE2](https://github.com/google/re2/wiki/Syntax). Эти выражения должны быть закодированы в Kusto как `string` литералы, и все правила заключения в кавычки Kusto применяются. Например, регулярное выражение `\A` соответствует началу строки и указывается в Kusto в качестве строкового литерала `"\\A"` (Обратите внимание на знак "лишний" обратной косой черты ( `\` )).
