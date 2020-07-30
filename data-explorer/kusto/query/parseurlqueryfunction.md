---
title: parse_urlquery () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается parse_urlquery () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d34ece3a945485b8a809089d030fa954b070a28
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346275"
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
 