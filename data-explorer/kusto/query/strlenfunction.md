---
title: strlen () - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны стрелен() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33bb1ea56ebc03dc7357264bcdf987c191478cbb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506874"
---
# <a name="strlen"></a>strlen()

Возвращает длину, в символах, строки ввода.

**Синтаксис**

`strlen(`*Источник*`)`

**Аргументы**

* *источник*: Строка источника, которая будет измерена для длины строки.

**Возвращает**

Возвращает длину, в символах, строки ввода.

**Примечания**

Каждый символ Unicode в `1`строке равен, включая суррогаты.
(например, китайские иероглифы будут подсчитаны один раз, несмотря на то, что он требует более одного значения в кодировании UTF-8).


**Примеры**

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