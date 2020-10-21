---
title: base64_decode_toarray () — обозреватель данных Azure
description: В этой статье описывается base64_decode_toarray () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 34a4601c35acb9c95e09e49d201b2e1cddaace8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252736"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Декодирует строку Base64 в массив длинных значений.

## <a name="syntax"></a>Синтаксис

`base64_decode_toarray(`*Строка*`)`

## <a name="arguments"></a>Аргументы

* *String*: входная строка для декодирования из Base64 в UTF8.

## <a name="returns"></a>Результаты

Возвращает массив длинных значений, декодированных из строки Base64.

* Сведения о декодировании строк Base64 в строку UTF-8 см. в разделе [base64_decode_tostring ()](base64_decode_tostringfunction.md) .
* Сведения о кодировании строк в строку Base64 см. в разделе [base64_encode_tostring ()](base64_encode_tostringfunction.md) .

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|куине|
|-----|
|[75 117 115 116 111]|

При попытке декодирования строки Base64, созданной из недопустимой кодировки UTF-8, будет возвращено значение NULL:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
