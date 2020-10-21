---
title: to_utf8 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается to_utf8 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c47c37dbbde7bd2276f1b5788dc6eb7b062f39a3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251264"
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
