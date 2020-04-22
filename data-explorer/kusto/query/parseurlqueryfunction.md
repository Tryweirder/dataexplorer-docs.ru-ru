---
title: parse_urlquery () - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает parse_urlquery () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f00fefcd6245528d7ae50d6046d97289a92317d
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744612"
---
# <a name="parse_urlquery"></a>parse_urlquery()

Возвращает `dynamic` объект, содержащий параметры запроса.

**Синтаксис**

`parse_urlquery(`*Запроса*`)`

**Аргументы**

* *запрос*: Строка представляет запрос URL.

**Возвращает**

Объект [динамической типы,](./scalar-data-types/dynamic.md) включающий параметры запроса.

**Пример**

```kusto
parse_urlquery("k1=v1&k2=v2&k3=v3")
```

приведет к:

```kusto
 {
    "Query Parameters":"{"k1":"v1", "k2":"v2", "k3":"v3"}",
 }
```

**Примечания**

* Формат ввода должен следовать стандартам запроса URL (ключевое значение& ...)
 