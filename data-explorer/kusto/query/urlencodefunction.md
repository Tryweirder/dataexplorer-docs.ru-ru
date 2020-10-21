---
title: url_encode () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается url_encode () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: a8c8e874fa4f6a1cb8c8731400e794e1359a4719
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240989"
---
# <a name="url_encode"></a>url_encode()

Функция преобразует символы входного URL-адреса в формат, который может быть передан через Интернет. 

Подробные сведения о кодировке и декодировании URL-адресов можно найти [здесь](https://en.wikipedia.org/wiki/Percent-encoding).
Отличается от [url_encode_component](./urlencodecomponentfunction.md) с помощью кодирования пробелов как "+", а не "20%" (см. Application/x-www-Form-UrlEncoded [здесь](https://en.wikipedia.org/wiki/Percent-encoding)).

## <a name="syntax"></a>Синтаксис

`url_encode(`*URL-адрес*`)`

## <a name="arguments"></a>Аргументы

* *URL-* адрес: входной URL-адрес (строка).  

## <a name="returns"></a>Результаты

URL-адрес (строка), преобразованный в формат, который может быть передан через Интернет.

## <a name="examples"></a>Примеры

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|оригинальный|кодировке|
|---|---|
|https://www.bing.com/hello словам|HTTPS %3 a %2 f %2 f www. Bing. com% 2fhello + Word|


 