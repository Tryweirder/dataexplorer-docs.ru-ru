---
title: base64_encode_tostring () — обозреватель данных Azure
description: В этой статье описывается base64_encode_tostring () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 6fca71cbc7cf33f49a9f4698553391a84ddbb728
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252661"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Кодирует строку как строку Base64.

## <a name="syntax"></a>Синтаксис

`base64_encode_tostring(`*Строка*`)`

## <a name="arguments"></a>Аргументы

* *String*: входная строка для кодирования в виде строки Base64.

## <a name="returns"></a>Результаты

Возвращает строку, закодированную как строка Base64.

* Сведения о декодировании строк Base64 в строки UTF-8 см. в разделе [base64_decode_tostring ()](base64_decode_tostringfunction.md) .
* Сведения о декодировании строк Base64 в массив длинных значений см. в разделе [base64_decode_toarray ()](base64_decode_toarrayfunction.md) .


## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|куине   |
|--------|
|S3VzdG8 =|

