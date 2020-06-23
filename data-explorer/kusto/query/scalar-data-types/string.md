---
title: Тип данных String — Azure обозреватель данных
description: В этой статье описывается тип данных String в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7c043a9b4d8b141d2dc45e88022e191e6483c35
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264816"
---
# <a name="the-string-data-type"></a>Тип данных String

`string`Тип данных представляет строку в Юникоде. Строки Kusto кодируются в кодировке UTF-8 и по умолчанию ограничены 1 МБ.

## <a name="string-literals"></a>Строковые литералы

Существует несколько способов кодирования литералов `string` типа данных.

* Заключите строку в двойные кавычки ( `"` ):`"This is a string literal. Single quote characters (') don't require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* Заключите строку в одинарные кавычки ( `'` ):`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

В двух приведенных выше представлениях символ обратной косой черты ( `\` ) обозначает экранирование.
Он используется для экранирования заключенных в кавычки символов, символов табуляции ( `\t` ), символов новой строки ( `\n` ) и самого себя ( `\\` ).

Также поддерживаются буквальные строковые литералы. В этой форме символ обратной косой черты ( `\` ) означает сам себя, а не escape-символ.

* Заключите в двойные кавычки ( `"` ):`@"This is a verbatim string literal that ends with a backslash\"`
* Заключить в одинарные кавычки ( `'` ):`@'This is a verbatim string literal that ends with a backslash\'`

Два строковых литерала в запросе, которые не находятся между ними или разделяются только пробелами и комментариями, автоматически присоединяются к форме нового строкового литерала.
Например, следующие выражения имеют длину `13` .

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

Как можно заметить, если строка заключена в двойные кавычки ( `"` ), символ одинарной кавычки ( `'` ) не требует экранирования, а также других способов. Этот метод упрощает отценки на строки в соответствии с контекстом.

## <a name="obfuscated-string-literals"></a>Маскированные строковые литералы

Система отслеживает запросы и сохраняет их в целях телеметрии и анализа.
Например, текст запроса может быть предоставлен владельцу кластера. Если текст запроса содержит секретные сведения, такие как пароли, то данные, которые должны храниться в частном порядке, могут быть утеряны. Чтобы предотвратить такую утечку, автор запроса может пометить определенные строковые литералы как **замаскированные строковые литералы**.
Такие литералы в тексте запроса автоматически заменяются числом звездочек ( `*` ), чтобы они не были доступны для последующего анализа.

> [!IMPORTANT]
> Пометьте все строковые литералы, содержащие секретные сведения, в виде скрытых строковых литералов.

Скрытый строковый литерал можно формировать с помощью "регулярного" строкового литерала, а `h` `H` перед ним — перед символом или. 

Пример:

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> Во многих случаях только часть строкового литерала является секретной. В таких случаях разделите литерал на несекретную часть и секретную часть. Затем отметьте только часть секрета как замаскированную.

Пример:

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```
