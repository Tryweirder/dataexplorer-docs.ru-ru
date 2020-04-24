---
title: base64_encode_fromarray () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается base64_encode_fromarray () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: f601463fd6751be7064892e70e5b2235f96a20ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518077"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Кодирует строку base64 из массива байтов.

**Синтаксис**

`base64_encode_fromarray(`*БайтсРейрей*`)`

**Аргументы**

* *BytesArray*: Массив входных байтов, который будет закодирован в строку base64.

**Возвращает**

Возвращает строку base64, закодированную из массива байтов.

* Для расшифровки базовых строк на строку UTF-8 см. [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Для кодирования строк на base64 [см. base64_encode_tostring()](base64_encode_tostringfunction.md)
* Эта функция является обратной [base64_decode_toarray()](base64_decode_toarrayfunction.md)

**Пример**

```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8|


Попытка кодировать строку base64 из недействительного массива байтов, сгенерированного из недействительной закодированной строки UTF-8, вернет нулевую строку:

```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||