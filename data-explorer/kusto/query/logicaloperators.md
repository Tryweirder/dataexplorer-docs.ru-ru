---
title: Логические (бинарные) операторы в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются логические операторы (бинарные) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 6d1fcf7b9786951fedbdbf45d9e2c20a765452dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248508"
---
# <a name="logical-binary-operators"></a>Бинарные (логические) операторы

Между двумя значениями типа поддерживаются следующие логические операторы `bool` :

> [!NOTE]
> Эти логические операторы иногда называются логическими операторами, а иногда — бинарными операторами. Имена являются синонимами.

|имя оператора;|Синтаксис|Значение|
|-------------|------|-------|
|Равенство     |`==`  |Возвращает `true` значение, если оба операнда имеют значения, отличные от NULL, и равны друг другу. В противном случае — значение `false`.|
|Неравенство   |`!=`  |Дает `true` значение, если один из операндов (или оба) либо оба являются неопределенными, либо они не равны друг другу. В противном случае — значение `true`.|
|Логическое и  |`and` |Возвращает значение `true` , если оба операнда имеют значение `true` .|
|Логическое или   |`or`  |Возвращает `true` , если один из операндов имеет значение `true` , независимо от другого операнда.|

> [!NOTE]
> Из-за поведения логического значения NULL `bool(null)` два логических значения NULL не равны и не равны (иными словами, `bool(null) == bool(null)` и `bool(null) != bool(null)` оба дают значение `false` ).
>
> С другой стороны, `and` / `or` значение NULL интерпретируется как эквивалентное `false` , так что `bool(null) or true` равно `true` , и `bool(null) and true` является `false` .