---
title: gzip_compress_to_base64_string Azure обозреватель данных
description: В этой статье описывается команда gzip_compress_to_base64_string () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: b2bed8fab33de57da6286a7ea49b9378669a9d5a
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373856"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string()

Выполняет сжатие Gzip и кодирует результат в Base64.


## <a name="syntax"></a>Синтаксис

`gzip_compress_to_base64_string("`*Input_string* "`)`

## <a name="arguments"></a>Аргументы

*Input_string* : входные данные `string` , строка для сжатия и кодировка Base64. Функция принимает один строковый аргумент.

## <a name="returns"></a>Возвращаемое значение

* Возвращает объект `string` , который представляет исходную строку, сжатую в формате gzip и в кодировке Base64. 
* Возвращает пустой результат в случае сбоя сжатия или кодирования.

## <a name="example"></a>Пример
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**Выходные данные:** 

| Еаебфадр/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA = = |

## <a name="next-steps"></a>Дальнейшие шаги

Чтобы получить исходную несжатую строку, используйте [gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) .
