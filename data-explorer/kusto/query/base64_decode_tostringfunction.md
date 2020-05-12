---
title: base64_decode_tostring () — обозреватель данных Azure
description: В этой статье описывается base64_decode_tostring () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: d2e1c5dbb845e67a5306ccb16c234de383d3ca69
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225349"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Декодирует строку Base64 в строку UTF-8.

**Синтаксис**

`base64_decode_tostring(`*Строка*`)`

**Аргументы**

* *String*: входная строка для декодирования из Base64 в строку UTF8-8.

**Возвращает**

Возвращает строку в кодировке UTF-8, декодированную из строки Base64.

* Сведения о декодировании строк Base64 в массив длинных значений см. в разделе [base64_decode_toarray ()](base64_decode_toarrayfunction.md) .
* Сведения о кодировке строк в строку Base64 см. в разделе [base64_encode_tostring ()](base64_encode_tostringfunction.md) .

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|куине|
|-----|
|Kusto|

При попытке декодирования строки Base64, созданной из недопустимой кодировки UTF-8, будет возвращено значение NULL:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
