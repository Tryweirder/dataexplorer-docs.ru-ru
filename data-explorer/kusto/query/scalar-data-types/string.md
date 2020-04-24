---
title: Тип строки данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается тип строки данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c040045ba7146cf56487ca3a8729372084d3bf2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509628"
---
# <a name="the-string-data-type"></a>Тип данных строки

Тип `string` данных представляет строку Unicode. (Строки Kusto закодированы в UTF-8 и по умолчанию ограничены 1 МБ.)

## <a name="string-literals"></a>Строковые литералы

Существует несколько способов кодирования буквальных данных `string` типа:

* Прилагая строку в двойных кавычках ( ):`"``"This is a string literal. Single quote characters (') do not require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* Прилагая строку в одно-кавычки ( ):`'``'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

В двух представлениях выше, backslash (`\`) характер указывает на побег.
Он используется, чтобы избежать прилагая цитаты`\t`символов, вкладка`\n`символов`\\`( ), Newline символов ( ), и сам ().

Дословные строки буквальные также поддерживаются. В этой форме, backslash`\`характер () выступает за себя, а не как побег характер:

* Прилагается в двойных`"`котировках ( ):`@"This is a verbatim string literal that ends with a backslash\"`
* Прилагается в одно-кавычки ( ):`'``@'This is a verbatim string literal that ends with a backslash\'`

Две строки буквальных в тексте запроса с ничего между ними, или разделены только whitespace и комментарии, автоматически скупаются вместе, чтобы сформировать новую строку буквального (до тех пор, пока такая замена не может быть сделано).
Например, следующие выражения все `13`урожаи:

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>Примеры

```kusto
// Simple string notation
print s1 = 'some string', s2 = "some other string"

// Strings that include single or double-quotes can be defined as follows
print s1 = 'string with " (double quotes)',
          s2 = "string with ' (single quotes)"

// Strings with '\' can be prefixed with '@' (as in c#)
print myPath1 = @'C:\Folder\filename.txt'

// Escaping using '\' notation
print s = '\\n.*(>|\'|=|\")[a-zA-Z0-9/+]{86}=='
```

Как видно, когда строка заключена в двойные цитаты ( ),`"`один-цитата (`'`) характер не требует побега и наоборот. Это упрощает цитирование строк в зависимости от контекста.

## <a name="obfuscated-string-literals"></a>Запутанная строка буквальные

Система отслеживает запросы и хранит их для целей телеметрии и анализа.
Например, текст запроса может быть доступен владельцу кластера. Если текст запроса содержит секретную информацию (например, пароли), это может привести к утечке информации, которая должна храниться в тайне. Чтобы этого не произошло, автор запроса может пометить определенные буквы строки как **запутанные буквы строки.**
Такие буквы в тексте запроса автоматически заменяются`*`несколькими символами звезды, так что они не доступны для последующего анализа.

> [!IMPORTANT]
> Настоятельно **рекомендуется,** чтобы все строки буквально, которые содержат секретную информацию, быть помечены как запутыванные строки буквальных.

Запутанная строка буквально может быть сформирована путем принятия "обычных" `h` строки `H` буквально, и prepending или символ перед ним. Пример:

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> Во многих случаях только часть строки буквально является тайной. Очень полезно в тех случаях разделить буквальную часть на несекретную и секретную часть, затем лишь отметить секретную часть как запутированную. Пример:

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```