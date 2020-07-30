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
ms.openlocfilehash: f330c10e95cdc36eae497811ef895ef827918b43
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346496"
---
# <a name="parse_command_line"></a>parse_command_line()

Анализирует строку командной строки в Юникоде и возвращает динамический массив аргументов командной строки.

## <a name="syntax"></a>Синтаксис

`parse_command_line(`*COMMAND_LINE*,*parser_type*`)`

## <a name="arguments"></a>Аргументы

* *COMMAND_LINE*: Командная строка для синтаксического анализа.
* *parser_type*: единственное поддерживаемое в настоящее время значение `"Windows"` , которое анализирует командную строку так же, как [коммандлинетоаргвв](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw).

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
