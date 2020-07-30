---
title: url_decode () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается url_decode () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0019e318b90f9626d9e55a593f19526cdc7cc9c7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350593"
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



 