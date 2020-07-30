---
title: url_encode_component () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается url_encode_component () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 44e77e2a0e34b66506852bd82d65e4a108b520fd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338462"
---
# <a name="url_encode_component"></a>url_encode_component()

Функция преобразует символы входного URL-адреса в формат, который может быть передан через Интернет. 

Подробные сведения о кодировке и декодировании URL-адресов можно найти [здесь](https://en.wikipedia.org/wiki/Percent-encoding).
Отличается от [url_encode](./urlencodefunction.md) путем кодирования пробелов как "20%", а не "+".

## <a name="syntax"></a>Синтаксис

`url_encode_component(`*URL-адрес*`)`

## <a name="arguments"></a>Аргументы

* *URL-* адрес: входной URL-адрес (строка).  

## <a name="returns"></a>Возвращаемое значение

URL-адрес (строка), преобразованный в формат, который может быть передан через Интернет.

## <a name="examples"></a>Примеры

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|оригинальный|кодировке|
|---|---|
|https://www.bing.com/helloсловам|HTTPS %3 a %2 f %2 f www. Bing. com% 2fhello% 20word|


 