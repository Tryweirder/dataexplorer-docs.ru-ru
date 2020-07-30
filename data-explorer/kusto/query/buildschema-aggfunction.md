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
ms.openlocfilehash: 2520849508c9cef829d7c8c07f22d3f8c64cfcea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348944"
---
# <a name="buildschema-aggregation-function"></a>буилдсчема () (агрегатная функция)

Возвращает минимальную схему, которая отменяет все значения *динамицекспр*.

* Может использоваться только в контексте статистической обработки [, внутри](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `buildschema(` *динамицекспр*`)`

## <a name="arguments"></a>Аргументы

* *Динамицекспр*: выражение, используемое для вычисления агрегата. Тип столбца параметра должен быть `dynamic` . 

## <a name="returns"></a>Результаты

Максимальное значение в *`Expr`* группе.

> [!TIP] 
> Если `buildschema(json_column)` выдается синтаксическая ошибка: *является ли `json_column` строка, а не динамический объект?* затем используйте `buildschema(parsejson(json_column))` .

## <a name="example"></a>Пример

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
    
