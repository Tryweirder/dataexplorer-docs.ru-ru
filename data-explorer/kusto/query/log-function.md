---
title: журнал () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается журнал () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7bd3c4f5c6b262587cab2327486945f5d78aaf02
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513283"
---
# <a name="log"></a>log()

`log()`возвращает естественную функцию logarithm.  

**Синтаксис**

`log(`*X*`)`

**Аргументы**

* *x*: Реальное число > 0.

**Возвращает**

* Естественный logarithm будет base-e logarithm: обратная естественной экспоненциальной функции (exp).
* `null`если аргумент отрицательный или нулевый или `real` не может быть преобразован в значение. 

**См. также:**

* Для общих (базовых-10) логарифмов см. [log10()](log10-function.md).
* Для баз-2 логарифмов [см. log2()](log2-function.md)