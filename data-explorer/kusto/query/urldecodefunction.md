---
title: url_decode () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны url_decode () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3ffa5052a2fc30387be118683ec1df6f34f7346f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505157"
---
# <a name="url_decode"></a>url_decode()

Функция преобразует закодированный URL в обычное представление URL. 

Подробную информацию о расшифровке и кодировании URL можно найти [здесь](https://en.wikipedia.org/wiki/Percent-encoding).

**Синтаксис**

`url_decode(`*Закодированный URL*`)`

**Аргументы**

* *закодированный URL:* закодированный URL (строка).  

**Возвращает**

URL (строка) в регулярном представлении.

**Примеры**

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|оригинальный|Декодированные|
|---|---|
|https%3a%2f%2fwww.bing.com%2f|https://www.bing.com/|



 