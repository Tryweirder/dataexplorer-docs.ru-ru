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
ms.openlocfilehash: 218f87a3c11695c4aa8135f98b0c445580de9ad0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349267"
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

