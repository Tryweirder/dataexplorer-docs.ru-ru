---
title: strlen () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается strlen () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4256218669685ec12a939156021803105e5ddfc6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248417"
---
# <a name="strlen"></a>strlen()

Возвращает длину входной строки в символах.

## <a name="syntax"></a>Синтаксис

`strlen(`*источника*`)`

## <a name="arguments"></a>Аргументы

* *Source*: исходная строка, которая будет измеряться для длины строки.

## <a name="returns"></a>Результаты

Возвращает длину входной строки в символах.

**Примечания**

Каждый символ Юникода в строке равен `1` , включая суррогаты.
(например, китайские символы будут учитываться один раз, несмотря на то, что в кодировке UTF-8 требуется более одного значения).


## <a name="examples"></a>Примеры

```kusto
print length = strlen("hello")
```

|length|
|---|
|5|

```kusto
print length = strlen("⒦⒰⒮⒯⒪")
```

|length|
|---|
|5|