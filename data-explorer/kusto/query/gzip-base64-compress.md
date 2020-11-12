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
ms.openlocfilehash: fffa39ca5fa41c065971b4feebfe60752b84ed59
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548875"
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

| H4sIAAAAAAAA/Веуаов/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA = = |

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы получить исходную несжатую строку, используйте [gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) .
* См. также [zlib_compress_to_base64_string ()](zlib-base64-compress.md)
