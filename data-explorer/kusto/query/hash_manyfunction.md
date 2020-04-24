---
title: hash_many() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается hash_many () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: e98f9d1d956d15cd7a61e7873f9b1dd34c6ae288
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514184"
---
# <a name="hash_many"></a>hash_many()

Возвращает комбинированное хэш-значение нескольких значений.

**Синтаксис**

`hash_many(`*s1* `,` *s2* `,` *s3 s3* ...»`)`

**Аргументы**

* *s1*, *s2*, ..., *sN*: входные значения, которые будут хэшированы вместе.

**Возвращает**

Совокупная хэш-значение данных скаляров.

**Примеры**

```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|Комбинированный|
|---|---|---|
|Привет|World|-1440138333540407281|