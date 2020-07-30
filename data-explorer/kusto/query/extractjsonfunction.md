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
ms.openlocfilehash: 21f366e8f211acf9c7c542b420006cfb23e683e7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348043"
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

## <a name="returns"></a>Результаты

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