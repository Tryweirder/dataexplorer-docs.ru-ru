---
title: base64_decode_toarray () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны base64_decode_toarray () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 80a702f112fd4d7b88b4011b3f615cf34acff62c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518196"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Расшифровывает строку base64 под набор длинных значений.

**Синтаксис**

`base64_decode_toarray(`*Строка*`)`

**Аргументы**

* *Строка*: Строка ввода, которая будет расшифрована с base64 до строки UTF8-8.

**Возвращает**

Возвращает массив длинных значений, закодированных из строки base64.

* Для расшифровки базовых строк на строку UTF-8 см. [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Для кодирования строк на base64 [см. base64_encode_tostring()](base64_encode_tostringfunction.md)

**Пример**

```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Квин|
|-----|
|[75,117,115,116,111]|

Попытка расшифровать строку base64, которая была создана из недействительного кодирования UTF-8, вернет нулевую строку:

```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||