---
title: екстрактжсон () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается екстрактжсон () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9668b173c6b3769113972be2c74382464e7d9819
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610582"
---
# <a name="extractjson"></a>extractjson()

Возвращает указанный элемент текста JSON с помощью выражения пути. 

При необходимости преобразует извлеченную строку к указанному типу.

```kusto
extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))
```

## <a name="syntax"></a>Синтаксис

`extractjson(`*jsonPath* `,` *источник данных*`)` 

## <a name="arguments"></a>Аргументы

* *jsonPath*: строка jsonPath, которая определяет метод доступа к документу JSON.
* *DataSource*: документ JSON.

## <a name="returns"></a>Возвращаемое значение

Эта функция выполняет запрос JsonPath к источнику данных dataSource, который содержит допустимую строку JSON. При необходимости это значение преобразуется в другой тип, в зависимости от третьего аргумента.

## <a name="example"></a>Пример

`[` `]` Нотация скобок нотататион и Dot ( `.` ) эквивалентна:

```kusto
T 
| extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) 

T
| extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) 
```

### <a name="json-path-expressions"></a>Выражения пути JSON

|Выражение пути|Описание|
|---|---|
|`$`|Корневой объект|
|`@`|Текущий объект|
|`.` либо `[ ]` | Дочерний|
|`[ ]`|Индекс массива|

*(В настоящее время подстановочные знаки, рекурсии, объединения и срезы не реализованы.)*


**Советы по улучшению производительности**

* Применяйте предложения where перед использованием `extractjson()`
* По возможности предпочтительнее использовать сопоставление с регулярным выражением и [extract](extractfunction.md) . Он выполняется гораздо быстрее и эффективнее, если JSON создается из шаблона.
* Используйте `parse_json()` , если требуется извлечь из JSON несколько значений.
* При возможности проведите анализ JSON при обработке, объявив динамический тип столбца.