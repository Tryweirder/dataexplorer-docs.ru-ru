---
title: to_utf8 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается to_utf8 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 891a2bb079136d9a7c21c1992b79e3e0eab4c970
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350678"
---
# <a name="to_utf8"></a>to_utf8()

Возвращает динамический массив символов Юникода входной строки (обратная операция make_string).

## <a name="syntax"></a>Синтаксис

`to_utf8(`*источника*`)`

## <a name="arguments"></a>Аргументы

* *Source*: исходная строка для преобразования.

## <a name="returns"></a>Результаты

Возвращает динамический массив символов Юникода, которые составляют строку, предоставленную данной функции.
См. раздел [`make_string()`](makestringfunction.md) )

## <a name="examples"></a>Примеры

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|маленькая|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|маленькая|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
