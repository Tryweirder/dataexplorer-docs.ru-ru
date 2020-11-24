---
title: Регулярные выражения в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются регулярные выражения в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.localizationpriority: high
ms.openlocfilehash: e4dda7ca499ac9fc9f90f6576758797d3f62a299
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513086"
---
# <a name="re2-syntax"></a>Синтаксис RE2

Синтаксис регулярных выражений RE2 описывает синтаксис библиотеки регулярных выражений, используемой Kusto (RE2).
Существует несколько функций в Kusto, которые выполняют сопоставление строк, выделение и извлечение с помощью регулярного выражения.

- [countof()](countoffunction.md)
- [extract()](extractfunction.md)
- [extract_all()](extractallfunction.md)
- [соответствует regex](datatypes-string-operators.md)
- [Оператор parse](parseoperator.md)
- [Replace ()](replacefunction.md)
- [Trim ()](trimfunction.md)
- [TrimEnd ()](trimendfunction.md)
- [TrimStart ()](trimstartfunction.md)

Синтаксис регулярных выражений, поддерживаемый Kusto, состоит из [библиотеки RE2](https://github.com/google/re2/wiki/Syntax). Эти выражения должны быть закодированы в Kusto как `string` литералы, и все правила заключения в кавычки Kusto применяются. Например, регулярное выражение `\A` соответствует началу строки и указывается в Kusto в качестве строкового литерала `"\\A"` (Обратите внимание на знак "лишний" обратной косой черты ( `\` )).
