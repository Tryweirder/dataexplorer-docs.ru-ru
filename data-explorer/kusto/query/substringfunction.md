---
title: substring () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается подстрока () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512049"
---
# <a name="substring"></a>substring()

Извлекает подстроку из исходной строки, начиная с некоторого индекса и заканчивая строкой.

При необходимости можно указать длину запрашиваемой подстроки.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>Синтаксис

`substring(`*Исходный код* `,` *стартингиндекс* [ `,` *Длина*]`)`

## <a name="arguments"></a>Аргументы

* *Source*— исходная строка, из которой будет взята подстрока.
* *стартингиндекс*: Отсчитываемый от нуля начальный символ запрашиваемой подстроки.
* *length*: необязательный параметр, который можно использовать для указания запрошенного количества символов в подстроке. 

**Примечания**

*стартингиндекс* может быть отрицательным числом, в этом случае подстрока будет извлечена из конца исходной строки.

## <a name="returns"></a>Возвращаемое значение

Подстрока для указанной строки. Подстрока начинается с позиции startingIndex (отсчитывается начиная с нуля) и продолжается до конца строки или до требуемого количества символов, если оно указано.

## <a name="examples"></a>Примеры

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```