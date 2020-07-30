---
title: make_string () — обозреватель данных Azure
description: В этой статье описывается make_string () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36d31e88a89f23006dac73b92777b13db4933d06
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346887"
---
# <a name="make_string"></a>make_string()

Возвращает строку, сформированную символами Юникода.
    
## <a name="syntax"></a>Синтаксис

`make_string (`*Arg1*[, *ArgN*]...`)`

## <a name="arguments"></a>Аргументы

* *Arg1* ... *ArgN*: выражения, являющиеся целыми числами (int или long), или динамическое значение, содержащее массив целых чисел.

* Эта функция получает до 64 аргументов.

## <a name="returns"></a>Результаты

Возвращает строку, состоящие из символов Юникода, значение codepoint которых указывается в аргументах этой функции. Входные данные должны состоять из допустимого кодовыми точкамиа Юникода.
Если какой-либо аргумент не сопоставлен с символом Юникода, функция возвращает значение `null` .

## <a name="examples"></a>Примеры

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|str|
|---|
|K<br>u<br>s<br>т<br>o|
