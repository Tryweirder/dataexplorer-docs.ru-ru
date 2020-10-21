---
title: буилдсчема () (агрегатная функция) — Azure обозреватель данных
description: В этой статье описывается буилдсчема () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d371b205363f320f666cfd92329219bb992fc69b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245273"
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
> Если `buildschema(json_column)` приводит к синтаксической ошибке:
>
> > *Является ли `json_column` строка, а не динамический объект?*
>
> затем используйте `buildschema(parsejson(json_column))` .

## <a name="example"></a>Пример

Предположим, что входной столбец имеет три динамических значения.

* `{"x":1, "y":3.5}`
* `{"x":"somevalue", "z":[1, 2, 3]}`
* `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`

Итоговая схема будет следующей:

```kusto
{ 
    "x":["int", "string"],
    "y":["double", {"w": "string"}],
    "z":{"`indexer`": ["int", "string"]},
    "t":{"`indexer`": "string"}
}
```

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

```output
Container ::= '{' Named-type* '}';
Named-type ::= (name | '"`indexer`"') ':' Type;
Type ::= Primitive-type | Union-type | Container;
Union-type ::= '[' Type* ']';
Primitive-type ::= "int" | "string" | ...;
```

Значения эквивалентны подмножеству аннотаций типа TypeScript, закодированных как динамическое значение Kusto. В Typescript пример схемы будет следующим:

```typescript
var someobject: 
{
    x?: (number | string),
    y?: (number | { w?: string}),
    z?: { [n:number] : (int | string)},
    t?: { [n:number]: string }
}
```
