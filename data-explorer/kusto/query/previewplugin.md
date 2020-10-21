---
title: Предварительный просмотр подключаемого модуля — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается подключаемый модуль предварительной версии в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 455ff1d4d3a42c09a39673028405d51b7acd1f5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251647"
---
# <a name="preview-plugin"></a>Подключаемый модуль preview

Возвращает таблицу с указанным числом строк из набора входных записей и общим числом записей во входном наборе записей.

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>Синтаксис

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

## <a name="returns"></a>Результаты

`preview`Подключаемый модуль возвращает две результирующие таблицы:
* Таблица с указанным числом строк вплоть до указанного числа.
  Например, приведенный выше пример запроса эквивалентен выполнению `T | take 50` .
* Таблица с одной строкой или столбцом, содержащей количество записей во входном наборе записей.
  Например, приведенный выше пример запроса эквивалентен выполнению `T | count` .

> [!TIP]
> Если `evaluate` перед ним стоит табличный источник, включающий сложный фильтр, или фильтр, который ссылается на большую часть столбцов исходной таблицы, предпочтительно использовать [`materialize`](materializefunction.md) функцию. Пример:

```kusto
let MaterializedT = materialize(T);
MaterializedT | evaluate preview(50)
```