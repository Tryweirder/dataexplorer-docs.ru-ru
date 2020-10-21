---
title: make_string () — обозреватель данных Azure
description: В этой статье описывается make_string () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8f3497e10c15bfd6df0337758d8dc3002419fa1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252242"
---
# <a name="make_string"></a>make_string()

Возвращает строку, сформированную символами Юникода.
    
## <a name="syntax"></a>Синтаксис

`make_string (`*Arg1*[, *ArgN*]... `)`

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
