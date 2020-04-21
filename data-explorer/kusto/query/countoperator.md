---
title: Оператор подсчета - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор подсчета в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: c0d0286919a68b6e58065e0a6fe7e0d24b1cdd5f
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610229"
---
# <a name="count-operator"></a>Оператор count

Возвращает количество записей в наборе входных записей.

**Синтаксис**

`T | count`

**Аргументы**

*T*— табличные данные, записи которых будут подсчитаны.

**Возвращает**

Эта функция возвращает таблицу с одной записью и столбцом типа `long`. Значение единственной ячейки — количество записей в *T*. 

**Пример**

```kusto
StormEvents | count
```
