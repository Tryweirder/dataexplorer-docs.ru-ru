---
title: Предварительный просмотр плагина - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается плагин предварительного просмотра в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18bda0a4348d0c0eb2776bf124c57397f318a989
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510988"
---
# <a name="preview-plugin"></a>предварительный просмотр плагина

Возвращает таблицу с указанным числом строк из набора входных записей и общее количество записей в наборе ввода.

```kusto
T | evaluate preview(50)
```

**Синтаксис**

`T``|` `evaluate` КоличествоOfRows *NumberOfRows* `preview(``)`

**Возвращает**

Плагин `preview` возвращает две таблицы результатов:
* Таблица с указанным числом строк.
  Например, приведенный выше запрос выборки эквивалентен запущенной. `T | take 50`
* Таблица с одной строкой/колонкой, удерживая количество записей в наборе ввода.
  Например, приведенный выше запрос выборки эквивалентен запущенной. `T | count`

**Советы**

Если `evaluate` предшествует табулярный источник, который включает сложный фильтр, или фильтр, который [`materialize`](materializefunction.md) ссылается на большинство столбцов исходной таблицы, предпочитают использовать функцию. Пример:

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```