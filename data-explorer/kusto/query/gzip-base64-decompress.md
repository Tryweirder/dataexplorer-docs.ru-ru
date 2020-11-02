---
title: gzip_decompress_from_base64_string () — обозреватель данных Azure
description: В этой статье описывается команда gzip_decompress_from_base64_string () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 19fc8c7ce74cfe632034722fda2b23c5105d013a
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148553"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string ()

Декодирует входную строку из Base64 и выполняет распаковку gzip.

## <a name="syntax"></a>Синтаксис

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>Аргументы

*Input_string* : входные данные `string` , которые были сжаты с помощью gzip, а затем кодируются в Base64. Функция принимает один строковый аргумент.

## <a name="returns"></a>Возвращаемое значение

* Возвращает объект `string` , представляющий исходную строку. 
* Возвращает пустой результат в случае сбоя распаковки или декодирования. 
    * Например, недопустимые сжатые с помощью gzip и Base 64 строки в кодировке будут возвращать пустой выход.

## <a name="examples"></a>Примеры

```kusto
print res=gzip_decompress_from_base64_string("eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA==")
```

**Выходные данные:**

|" 1234567890qwertyuiop "|

Пример недопустимых входных данных:

```kusto
print res=gzip_decompress_from_base64_string("x0x0x0")
```

**Выходные данные:**
>||

## <a name="next-steps"></a>Дальнейшие действия

Создайте сжатую входную строку с помощью [gzip_compress_to_base64_string ()](gzip-base64-compress.md).
