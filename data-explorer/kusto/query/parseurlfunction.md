---
title: parse_url () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается parse_url () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ce68151270369bd7dc0898b98029bacddf0c243
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248633"
---
# <a name="parse_url"></a>parse_url()

Анализирует абсолютный URL-адрес `string` и возвращает `dynamic` объект, содержащий части URL-адреса.


## <a name="syntax"></a>Синтаксис

`parse_url(`*URL-адрес*`)`

## <a name="arguments"></a>Аргументы

* *URL-адрес*: строка представляет URL-адрес или часть URL-адреса.

## <a name="returns"></a>Результаты

Объект типа [dynamic](./scalar-data-types/dynamic.md) , включающий компоненты URL-адреса: схема, узел, порт, путь, имя пользователя, пароль, параметры запроса, фрагмент.

## <a name="example"></a>Пример

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

результат будет

```
 {
    "Scheme":"scheme",
    "Host":"host",
    "Port":"1234",
    "Path":"this/is/a/path",
    "Username":"username",
    "Password":"password",
    "Query Parameters":"{"k1":"v1", "k2":"v2"}",
    "Fragment":"fragment"
 }
```