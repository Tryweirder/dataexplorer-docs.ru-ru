---
title: zlib_decompress_from_base64_string () — обозреватель данных Azure
description: В этой статье описывается команда zlib_decompress_from_base64_string () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: e2163b3fe5460a660579889a589acd1e5fd965cd
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573531"
---
# <a name="zlib_decompress_from_base64_string"></a>zlib_decompress_from_base64_string ()

Декодирует входную строку из Base64 и выполняет zlib распаковку.

> [!NOTE]
> Единственный поддерживаемый размер Windows — 15.

## <a name="syntax"></a>Синтаксис

`zlib_decompress_from_base64_string('input_string')`

## <a name="arguments"></a>Аргументы

*Input_string*: входные данные `string` , которые были сжаты с помощью zlib, а затем кодируются в Base64. Функция принимает один строковый аргумент.

## <a name="returns"></a>Возвращаемое значение

* Возвращает объект `string` , представляющий исходную строку. 
* Возвращает пустой результат в случае сбоя распаковки или декодирования. 
    * Например, недопустимые zlib-сжатые строки и базовые 64 в кодировке будут возвращать пустые выходные данные.

## <a name="examples"></a>Примеры

```kusto
print zcomp = zlib_decompress_from_base64_string("eJwLSS0uUSguKcrMS1cwNDIGACxqBQ4=")
```

**Выходные данные:**

| Тестовая строка 123 |

Пример недопустимых входных данных:

```kusto
print zcomp = zlib_decompress_from_base64_string("x0x0x0")
```

**Проверки**
||

## <a name="next-steps"></a>Дальнейшие действия

Создайте сжатую входную строку с помощью [zlib_compress_to_base64_string ()](zlib-base64-compress.md).