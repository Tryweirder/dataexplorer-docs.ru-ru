---
title: strlen () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается strlen () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d28eae6852faedf2c6071164d8f80f9c3567602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350899"
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