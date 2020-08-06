---
title: Предварительный просмотр подключаемого модуля — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается подключаемый модуль предварительной версии в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7c4ee69c4f82c25c6f4cf7d4b63ad9a659892a28
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803001"
---
# <a name="preview-plugin"></a>Подключаемый модуль preview

Возвращает таблицу с указанным числом строк из набора входных записей и общим числом записей во входном наборе записей.

```kusto
T | evaluate preview(50)
```

## <a name="syntax"></a>Синтаксис

`T` `|` `evaluate` `preview(` *NumberOfRows* `)`

## <a name="returns"></a>Возвращаемое значение

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