---
title: url_encode () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается url_encode () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 8ccc93286073003bdaf8324611888d32f60910fb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350576"
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
|https://www.bing.com/helloсловам|HTTPS %3 a %2 f %2 f www. Bing. com% 2fhello + Word|


 