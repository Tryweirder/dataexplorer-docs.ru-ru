---
title: substring() в Azure Data Explorer | Документация Майкрософт
description: В этой статье описана функция substring() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7b2f2dc18fe12f4bd07b638b6c3ca32d95a10618
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512049"
---
# <a name="substring"></a>substring()

Извлекает подстроку из строки источника, начиная с определенного индекса и заканчивая концом строки.

При необходимости можно указать длину запрашиваемой подстроки.

```kusto
substring("abcdefg", 1, 2) == "bc"
```

## <a name="syntax"></a>Синтаксис

`substring(`*source*`,` *startingIndex* [`,` *length*]`)`

## <a name="arguments"></a>Аргументы

* *source*. Исходная строка, из которой будет извлечена подстрока.
* *startingIndex.* Отсчитываемая от нуля позиция первого знака запрашиваемой подстроки.
* *length*: Необязательный параметр, который указывает требуемое количество знаков в подстроке. 

**Примечания**

Значение *startingIndex* может быть отрицательным числом. В этом случае подстрока будет извлекаться, начиная с конца исходной строки.

## <a name="returns"></a>Возвращаемое значение

Подстрока для указанной строки. Подстрока начинается с позиции startingIndex (отсчитывается начиная с нуля) и продолжается до конца строки или до требуемого количества символов, если оно указано.

## <a name="examples"></a>Примеры

```kusto
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
substring("123456", -2, 2)    // 56
```