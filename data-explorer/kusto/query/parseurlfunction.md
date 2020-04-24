---
title: parse_url () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается parse_url () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfc093964ce5b91acc01f798f8f62651266ab153
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511498"
---
# <a name="parse_url"></a>parse_url()

Сравнивает абсолютный `string` `dynamic` URL и возвращает объект, содержащий части URL.


**Синтаксис**

`parse_url(`*Url*`)`

**Аргументы**

* *URL:* Строка представляет URL или часть запроса URL.

**Возвращает**

Объект [динамической](./scalar-data-types/dynamic.md) тип, который включал компоненты URL: Схема, Хост, Порт, Путь, Имя пользователя, Пароль, Параметры запроса, Фрагмент.

**Пример**

```kusto
T | extend Result = parse_url("scheme://username:password@host:1234/this/is/a/path?k1=v1&k2=v2#fragment")
```

приведет к

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