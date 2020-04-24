---
title: экстракт () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается выдержка () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 398c79ddcd362f3c09fea18accd082c0eb3b1fc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515374"
---
# <a name="extract"></a>extract()

Определяет соответствие [регулярного выражения](./re2.md) из текстовой строки. 

Дополнительно преобразуйте извлеченную подстроку в указанный тип.

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

**Синтаксис**

`extract(`*regex* `,` *captureGroup* `,` `,` *текст* - *типLiteral*`)`

**Аргументы**

* *regex*: [Регулярное выражение](./re2.md).
* *captureGroup*: `int` Положительная константа, указывающая группу захвата для извлечения. 0 означает полное совпадение, 1 — значение, соответствующее первой '('круглой скобке')' в регулярном выражении, 2 или более — последующим круглым скобкам.
* *текст*: `string` A для поиска.
* *typeLiteral*: Необязательный тип буквальный `typeof(long)`(например, ). Если указан, то извлеченная подстрока преобразуется в этот тип. 

**Возвращает**

Если *regex* находит соответствие в *text*, то возвращается подстрока, которая сравнивалась с указанной в группе записи *captureGroup*, при необходимости преобразованная в тип *typeLiteral*.

Если соответствия нет или не удается выполнить преобразование типа, возвращается `null`. 

**Примеры**

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