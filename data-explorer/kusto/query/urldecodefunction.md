---
title: url_decode () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается url_decode () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b58fea5d367cf31b495b23a09bc0a0dcb6bb95c6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251864"
---
# <a name="url_decode"></a>url_decode()

Функция преобразует закодированный URL-адрес в обычное представление URL-адреса. 

Подробные сведения о декодировании и кодировании URL-адресов можно найти [здесь](https://en.wikipedia.org/wiki/Percent-encoding).

## <a name="syntax"></a>Синтаксис

`url_decode(`*закодированный URL-адрес*`)`

## <a name="arguments"></a>Аргументы

* *закодированный URL-* адрес: закодированный URL-адрес (строка).  

## <a name="returns"></a>Результаты

URL-адрес (строка) в обычном представлении.

## <a name="examples"></a>Примеры

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|оригинальный|декодировать|
|---|---|
|HTTPS %3 a %2 f %2 f www. Bing. com% 2F|https://www.bing.com/|



 