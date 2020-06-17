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
ms.openlocfilehash: ffef2fd609075b0d5e5af5c4064e079c27cd8c94
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818501"
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
* Сведения о декодировании строк в строку Base64 см. в разделе [base64_encode_tostring ()](base64_encode_tostringfunction.md) .

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
