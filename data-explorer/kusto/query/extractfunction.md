---
title: extract() в Azure Data Explorer | Документация Майкрософт
description: В этой статье описана функция extract() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 483c926d60abef120de2a355a6fa040b9608cd7a
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513052"
---
# <a name="extract"></a>extract()

Определяет соответствие [регулярного выражения](./re2.md) из текстовой строки. 

При необходимости преобразуйте извлеченную подстроку в указанный тип.

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>Синтаксис

`extract(`*regex*`,` *captureGroup*`,` *text* [`,` *typeLiteral*]`)`

## <a name="arguments"></a>Аргументы

* *regex*: [регулярное выражение](./re2.md).
* *captureGroup*: положительная константа `int`, указывающая извлекаемую группу записи. 0 означает полное совпадение, 1 — значение, соответствующее первой '('круглой скобке')' в регулярном выражении, 2 или более — последующим круглым скобкам.
* *text*: `string` для поиска.
* *typeLiteral*: необязательный литерал типа (например, `typeof(long)`). Если указан, то извлеченная подстрока преобразуется в этот тип. 

## <a name="returns"></a>Возвращаемое значение

Если *regex* находит соответствие в *text*, то возвращается подстрока, которая сравнивалась с указанной в группе записи *captureGroup*, при необходимости преобразованная в тип *typeLiteral*.

Если соответствия нет или не удается выполнить преобразование типа, возвращается `null`. 

## <a name="examples"></a>Примеры

В примере строки `Trace` производится поиск определения `Duration`. Соответствие преобразуется в `real`, а затем умножается на константу времени (`1s`), чтобы для `Duration` был задан тип `timespan`. В этом примере он равен 123,45 секунды:

```kusto
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Этот пример аналогичен `substring(Text, 2, 4)`.

```kusto
extract("^.{2,2}(.{4,4})", 1, Text)
```