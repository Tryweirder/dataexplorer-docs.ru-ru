---
title: parse_command_line () — обозреватель данных Azure
description: В этой статье описывается parse_command_line () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: 0296b41dc10092f0b274491c3fab3355fc82a2d9
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2020
ms.locfileid: "85518146"
---
# <a name="parse_command_line"></a>parse_command_line ()

Анализирует строку командной строки в Юникоде и возвращает динамический массив аргументов командной строки.

**Синтаксис**

`parse_command_line(`*COMMAND_LINE*,*parser_type*`)`

**Аргументы**

* *COMMAND_LINE*: Командная строка для синтаксического анализа.
* *parser_type*: единственное поддерживаемое в настоящее время значение `"Windows"` , которое анализирует командную строку так же, как [коммандлинетоаргвв](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

**Возвращает**

Динамический массив аргументов командной строки.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Результат|
|---|
|["echo", "Hello World!"]|
