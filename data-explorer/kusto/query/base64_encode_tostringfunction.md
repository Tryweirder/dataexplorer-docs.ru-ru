---
title: base64_encode_tostring () — обозреватель данных Azure
description: В этой статье описывается base64_encode_tostring () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: c414f1bdb83850bc6ec6065314bc7c8662ab0ed2
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717093"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Кодирует строку как строку Base64.

**Синтаксис**

`base64_encode_tostring(`*Строка*`)`

**Аргументы**

* *String*: входная строка для кодирования в виде строки Base64.

**Возвращает**

Возвращает строку, закодированную как строка Base64.

* Сведения о декодировании строк Base64 в строки UTF-8 см. в разделе [base64_decode_tostring ()](base64_decode_tostringfunction.md) .
* Сведения о декодировании строк Base64 в массив длинных значений см. в разделе [base64_decode_toarray ()](base64_decode_toarrayfunction.md) .


**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|куине   |
|--------|
|S3VzdG8 =|

