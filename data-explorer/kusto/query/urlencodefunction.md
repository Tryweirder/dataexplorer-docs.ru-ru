---
title: url_encode () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны url_encode() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 913be2d20af413f8ba89192f4db57e60fc6d7b27
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505055"
---
# <a name="url_encode"></a>url_encode()

Функция преобразует символы URL-адреса ввода в формат, который может передаваться через Интернет. 

Подробную информацию о кодировании и расшифровке URL можно найти [здесь](https://en.wikipedia.org/wiki/Percent-encoding).
Отличается от [url_encode_component](./urlencodecomponentfunction.md) кодированием пространств как «я», а не как «20%» (см. приложение/x-www-form-urlencoded [здесь).](https://en.wikipedia.org/wiki/Percent-encoding)

**Синтаксис**

`url_encode(`*Url*`)`

**Аргументы**

* *URL*: URL-адрес (строка).  

**Возвращает**

URL (строка) преобразуется в формат, который может передаваться через Интернет.

**Примеры**

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|оригинальный|Закодированные|
|---|---|
|https://www.bing.com/helloслово/|https%3a%2f%2fwww.bing.com%2fhello|


 