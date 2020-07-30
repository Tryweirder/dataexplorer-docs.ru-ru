---
title: base64_decode_toarray () — обозреватель данных Azure
description: В этой статье описывается base64_decode_toarray () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 4cfe690b5ee2d32462552fb90300c9e3168b1f1d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349318"
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

|Пусто|
|-----|
||
