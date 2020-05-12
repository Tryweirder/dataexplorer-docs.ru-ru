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
ms.openlocfilehash: eda367dfeaab15dc5249fd860596964c597a1bcd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225417"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Декодирует строку Base64 в массив длинных значений.

**Синтаксис**

`base64_decode_toarray(`*Строка*`)`

**Аргументы**

* *String*: входная строка для декодирования из Base64 в строку UTF8-8.

**Возвращает**

Возвращает массив значений типа Long, екодед из строки Base64.

* Сведения о декодировании строк Base64 в строку UTF-8 см. в разделе [base64_decode_tostring ()](base64_decode_tostringfunction.md) .
* Сведения о кодировке строк в строку Base64 см. в разделе [base64_encode_tostring ()](base64_encode_tostringfunction.md) .

**Пример**

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
