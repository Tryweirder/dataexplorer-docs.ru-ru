---
title: base64_encode_fromarray () — обозреватель данных Azure
description: В этой статье описывается base64_encode_fromarray () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 88d9f5b26273fa91ae64ca637b8fe4e2650a8539
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252700"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Кодирует строку Base64 из массива байтов.

## <a name="syntax"></a>Синтаксис

`base64_encode_fromarray(`*битесаррай*`)`

## <a name="arguments"></a>Аргументы

* *Битесаррай*: массив входных байтов для кодирования в строку Base64.

## <a name="returns"></a>Результаты

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
