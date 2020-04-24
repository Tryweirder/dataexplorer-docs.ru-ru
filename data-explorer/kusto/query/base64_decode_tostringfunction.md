---
title: base64_decode_tostring() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается base64_decode_tostring () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a821fb07d62d5bca3982cc4c48b8e0457641f3f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518179"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Декодирует строку base64 к строке UTF-8

**Синтаксис**

`base64_decode_tostring(`*Строка*`)`

**Аргументы**

* *Строка*: Строка ввода, которая будет расшифрована с base64 до строки UTF8-8.

**Возвращает**

Возвращает строку UTF-8, расшифрованную из строки base64.

* Для расшифровки базовых строк на массив длинных значений см. [base64_decode_toarray()](base64_decode_toarrayfunction.md)
* Для кодирования строк на base64 [см. base64_encode_tostring()](base64_encode_tostringfunction.md)

**Пример**

```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Квин|
|-----|
|Kusto|

Попытка расшифровать строку base64, которая была создана из недействительного кодирования UTF-8, вернет нулевую строку:

```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||