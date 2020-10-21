---
title: parse_urlquery () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается parse_urlquery () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1092edfaca8c6789ec6c0dc478fb27505531b1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244698"
---
# <a name="parse_urlquery"></a>parse_urlquery()

Возвращает `dynamic` объект, содержащий параметры запроса.

## <a name="syntax"></a>Синтаксис

`parse_urlquery(`*Выбор*`)`

## <a name="arguments"></a>Аргументы

* *запрос*: строка представляет запрос URL-адреса.

## <a name="returns"></a>Результаты

Объект типа [dynamic](./scalar-data-types/dynamic.md) , содержащий параметры запроса.

## <a name="example"></a>Пример

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

результат будет следующим:

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**Примечания**

* Формат входных данных должен соответствовать стандартам URL-запросов (ключ = значение&...)
 