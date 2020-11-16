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
ms.openlocfilehash: da8fe0bfedf5766f4599509e83fa1c7c050d069f
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638994"
---
# <a name="parse_command_line"></a>parse_command_line()

Анализирует строку командной строки в Юникоде и возвращает динамический массив аргументов командной строки.

## <a name="syntax"></a>Синтаксис

`parse_command_line(`*COMMAND_LINE* , *parser_type*`)`

## <a name="arguments"></a>Аргументы

* *COMMAND_LINE* : Командная строка для синтаксического анализа.
* *parser_type* : единственное поддерживаемое в настоящее время значение `"windows"` , которое анализирует командную строку так же, как [коммандлинетоаргвв](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

## <a name="returns"></a>Возвращаемое значение

Динамический массив аргументов командной строки.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Результат|
|---|
|["echo", "Hello World!"]|
