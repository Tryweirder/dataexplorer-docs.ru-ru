---
title: extractjson() - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается extractjson () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6177a1c8a6ed4390093e6f6fd24c5f5e9fd04f8a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515340"
---
# <a name="extractjson"></a>extractjson()

Возвращает указанный элемент текста JSON с помощью выражения пути. 

При необходимости преобразует извлеченную строку к указанному типу.

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

**Синтаксис**

`extractjson(`*данные jsonPathИсточникИсточник* `,` *dataSource*`)` 

**Аргументы**

* *jsonPath*: строка JsonPath, которая определяет аксессуар в документе JSON.
* *dataSource*: Документ JSON.

**Возвращает**

Эта функция выполняет запрос JsonPath к источнику данных dataSource, который содержит допустимую строку JSON. При необходимости это значение преобразуется в другой тип, в зависимости от третьего аргумента.

**Пример**

Нотаация `[`кронштейна`]` `.`и точка ( нотация эквивалентны:

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>Выражения пути JSON

|||
|---|---|
|`$`|Корневой объект|
|`@`|Текущий объект|
|`.` или `[ ]` | Дочерний|
|`[ ]`|Индекс массива|

*(В настоящее время подстановочные знаки, рекурсии, объединения и срезы не реализованы.)*


**Советы по улучшению производительности**

* Применяйте предложения where перед использованием `extractjson()`
* По возможности предпочтительнее использовать сопоставление с регулярным выражением и [extract](extractfunction.md) . Он выполняется гораздо быстрее и эффективнее, если JSON создается из шаблона.
* Используйте `parse_json()` , если требуется извлечь из JSON несколько значений.
* При возможности проведите анализ JSON при обработке, объявив динамический тип столбца.