---
title: Логические (двоичные) операторы - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны логические (двоичные) операторы в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 53505067b93234aa89849e1c66fe2f77a58e0f26
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513096"
---
# <a name="logical-binary-operators"></a>Бинарные (логические) операторы

Следующие логические операторы поддерживаются `bool` между двумя значениями типа:

> [!NOTE]
> Эти логические операторы иногда называются операторами Boolean, а иногда и бинарными операторами. Имена все синонимы.

|имя оператора;|Синтаксис|Значение|
|-------------|------|-------|
|Равенство     |`==`  |Урожайность, `true` если оба недействительныи и равны друг другу. В противном случае — `false`.|
|Неравенство   |`!=`  |Урожайность, `true` если одна (или оба) из операнд недействительны, или они не равны друг другу. В противном случае — `true`.|
|Логично и  |`and` |Урожайность, `true` если оба неперада . `true`|
|Логические или   |`or`  |Урожайность, `true` если один из operands, `true`независимо от других опер.|

> [!NOTE]
> Из-за поведения нулевого значения `bool(null)`Boolean, два минлевых значения Boolean не являются `bool(null) == bool(null)` ни `bool(null) != bool(null)` равными, `false`ни неравными (другими словами, и оба дают значение).
>
> С другой `and` / `or` стороны, относиться к `false`нулю значение как эквивалент, `bool(null) or true` так что, `true`и `bool(null) and true` есть `false`.