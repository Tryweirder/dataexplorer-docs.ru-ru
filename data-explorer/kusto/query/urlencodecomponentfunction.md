---
title: url_encode_component () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается url_encode_component () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 6740effd6a6117a2e63b5d03f09b38a723055f30
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241132"
---
# <a name="url_encode_component"></a>url_encode_component()

Функция преобразует символы входного URL-адреса в формат, который может быть передан через Интернет. 

Подробные сведения о кодировке и декодировании URL-адресов можно найти [здесь](https://en.wikipedia.org/wiki/Percent-encoding).
Отличается от [url_encode](./urlencodefunction.md) путем кодирования пробелов как "20%", а не "+".

## <a name="syntax"></a>Синтаксис

`url_encode_component(`*URL-адрес*`)`

## <a name="arguments"></a>Аргументы

* *URL-* адрес: входной URL-адрес (строка).  

## <a name="returns"></a>Результаты

URL-адрес (строка), преобразованный в формат, который может быть передан через Интернет.

## <a name="examples"></a>Примеры

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|оригинальный|кодировке|
|---|---|
|https://www.bing.com/hello словам|HTTPS %3 a %2 f %2 f www. Bing. com% 2fhello% 20word|


 