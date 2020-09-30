---
title: zlib_compress_to_base64_string Azure обозреватель данных
description: В этой статье описывается команда zlib_compress_to_base64_string () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: c43ffcbb449c6fc8a4c01b7a032df50c40829702
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573536"
---
# <a name="zlib_compress_to_base64_string"></a>zlib_compress_to_base64_string ()

Выполняет сжатие zlib и кодирует результат в Base64.

> [!NOTE]
> Единственный поддерживаемый размер Windows — 15.

## <a name="syntax"></a>Синтаксис

`zlib_compress_to_base64_string('input_string')`

## <a name="arguments"></a>Аргументы

*Input_string*: входные данные `string` , строка для сжатия и кодировка Base64. Функция принимает один строковый аргумент.

## <a name="returns"></a>Возвращаемое значение

* Возвращает объект `string` , который представляет исходную строку с zlib и кодировкой base64. 
* Возвращает пустой результат в случае сбоя сжатия или кодирования.

## <a name="example"></a>Пример

### <a name="using-kusto"></a>Использование Kusto

```kusto
print zcomp = zlib_compress_to_base64_string("1234567890qwertyuiop")
```

**Выходные данные:** | " Еаебфадр/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGdw = = "|

### <a name="using-python"></a>Использование Python

Сжатие можно выполнить с помощью других средств, например Python: 

```python
print(base64.b64encode(zlib.compress(b'<original_string>')))
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы получить исходную несжатую строку, используйте [zlib_decompress_from_base64_string ()](zlib-base64-decompress.md) .