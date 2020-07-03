---
title: буилдсчема () (агрегатная функция) — Azure обозреватель данных
description: В этой статье описывается буилдсчема () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cf21443beffb327e2708b8990017ac37fbbc8d21
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902054"
---
# <a name="buildschema-aggregation-function"></a>буилдсчема () (агрегатная функция)

Возвращает минимальную схему, которая отменяет все значения *динамицекспр*.

* Может использоваться только в контексте статистической обработки [, внутри](summarizeoperator.md)

**Синтаксис**

суммировать `buildschema(` *динамицекспр*`)`

**Аргументы**

* *Динамицекспр*: выражение, используемое для вычисления агрегата. Тип столбца параметра должен быть `dynamic` . 

**Возвращает**

Максимальное значение в *`Expr`* группе.

> [!TIP] 
> Если `buildschema(json_column)` выдается синтаксическая ошибка: *является ли `json_column` строка, а не динамический объект?* затем используйте `buildschema(parsejson(json_column))` .

**Пример**

Предположим, что входной столбец имеет три динамических значения.

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

* Корневой объект — это контейнер с четырьмя свойствами, именуемыми x, y, z и t.
* Свойство с именем "x", которое может иметь тип "int" или тип "String".
* Свойство с именем "y", которое может иметь тип "double", или другой контейнер со свойством "w" типа "String".
* Ключевое слово ``indexer`` указывает, что z и t являются массивами.
* Каждый элемент массива "z" имеет тип "int" или тип "String".
* t представляет собой массив строк.
* Каждое свойство является необязательным, и любой массив может быть пустым.

### <a name="schema-model"></a>Модель схемы

Синтаксис возвращаемой схемы следующий:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Значения эквивалентны подмножеству аннотаций типа TypeScript, закодированных как динамическое значение Kusto. В Typescript пример схемы будет следующим:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }
    
