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
ms.openlocfilehash: a13e3def33ea7098a48db7ffefa4406097863c2a
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342728"
---
# <a name="parse_command_line"></a>parse_command_line()

Анализирует строку командной строки в Юникоде и возвращает динамический массив аргументов командной строки.

## <a name="syntax"></a>Синтаксис

`parse_command_line(`*COMMAND_LINE*,*parser_type*`)`

## <a name="arguments"></a>Аргументы

* *COMMAND_LINE*: Командная строка для синтаксического анализа.
* *parser_type*: единственное поддерживаемое в настоящее время значение `"Windows"` , которое анализирует командную строку так же, как [коммандлинетоаргвв](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

## <a name="returns"></a>Результаты

Динамический массив аргументов командной строки.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Результат|
|---|
|["echo", "Hello World!"]|