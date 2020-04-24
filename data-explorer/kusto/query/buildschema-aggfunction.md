---
title: buildschema() (функция агрегации) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана сборка () (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d0faceae970034ae96ced2b8958af4f16cb5dff4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517295"
---
# <a name="buildschema-aggregation-function"></a>buildschema() (функция агрегации)

Возвращает минимальную схему, которая допускает все значения *DynamicExpr*.

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `buildschema(` *DynamicExpr*`)`

**Аргументы**

* *DynamicExpr*: Выражение, которое будет использоваться для расчета агрегации. Тип столбца параметра должен быть `dynamic`. 

**Возвращает**

Максимальное значение *Expr* по всей группе.

> [!TIP] 
> Если `buildschema(json_column)` дает ошибку синтаксиса: *Является ли ваша `json_column` строка, а не динамический объект?* Если это так, `buildschema(parsejson(json_column))`вам нужно использовать .

**Пример**

Предположим, что входной столбец имеет три динамических значения:

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

Итоговая схема будет следующей:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Схема указывает следующее:

* Корневой объект представляет собой контейнер с четырьмя свойствами x, y, z и t.
* Свойство x, которое может быть типа int или string.
* Свойство y, которое может либо типа double, либо другой контейнер со свойством w типа string.
* Ключевое слово ``indexer`` указывает, что z и t являются массивами.
* Каждый элемент в массиве z имеет тип int или string.
* t представляет собой массив строк.
* Каждое свойство является необязательным, и любой массив может быть пустым.

### <a name="schema-model"></a>Модель схемы

Синтаксис возвращаемой схемы следующий:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Они эквивалентны подмнозам аннотаций типа TypeScript, кодированным как динамическое значение Kusto. В Typescript пример схемы будет следующим:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }