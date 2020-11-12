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
ms.openlocfilehash: a35fd4ed2f43c991aa08e2e1594103cb5f5bd7a7
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548892"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string()

Декодирует входную строку из Base64 и выполняет распаковку gzip.

## <a name="syntax"></a>Синтаксис

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>Аргументы

*Input_string* : входные данные `string` , которые были сжаты с помощью gzip, а затем кодируются в Base64. Функция принимает один строковый аргумент.

> [!NOTE]
> Эта функция проверяет обязательные поля заголовка gzip (ID1, ID2 и CM) и возвращает пустой выходной файл, если какое-либо из этих полей содержит неправильные значения.
> Необязательные поля заголовка не поддерживаются. Как ФЛГ, так и XFL должны быть нулевыми.


## <a name="returns"></a>Возвращаемое значение

* Возвращает объект `string` , представляющий исходную строку. 
* Возвращает пустой результат в случае сбоя распаковки или декодирования. 
    * Например, недопустимые сжатые с помощью gzip и Base 64 строки в кодировке будут возвращать пустой выход.

## <a name="examples"></a>Примеры

```kusto
print res=gzip_decompress_from_base64_string("H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA==")
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

* Создайте сжатую входную строку с помощью [gzip_compress_to_base64_string ()](gzip-base64-compress.md).
* См. также [zlib_decompress_from_base64_string](zlib-base64-decompress.md).
