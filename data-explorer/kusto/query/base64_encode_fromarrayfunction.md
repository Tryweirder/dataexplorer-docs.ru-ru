---
title: base64_encode_fromarray () — обозреватель данных Azure
description: В этой статье описывается base64_encode_fromarray () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: ede35223b9cc57fddc5318659e6902130a80c011
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349284"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Кодирует строку Base64 из массива байтов.

## <a name="syntax"></a>Синтаксис

`base64_encode_fromarray(`*битесаррай*`)`

## <a name="arguments"></a>Аргументы

* *Битесаррай*: массив входных байтов для кодирования в строку Base64.

## <a name="returns"></a>Возвращаемое значение

Возвращает строку в кодировке Base64, закодированную из массива байтов.

* Сведения о декодировании строк Base64 в строку UTF-8 см. в разделе [base64_decode_tostring ()](base64_decode_tostringfunction.md) .
* Сведения о кодировке строк в строку Base64 см. в разделе [base64_encode_tostring ()](base64_encode_tostringfunction.md) .
* Эта функция является инверсией [base64_decode_toarray ()](base64_decode_toarrayfunction.md)

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8 =|


Попытка кодирования строки Base64 из недопустимого массива байтов, созданного из недопустимой строки в кодировке UTF-8, вернет значение NULL:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||
