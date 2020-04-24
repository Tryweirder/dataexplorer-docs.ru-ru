---
title: log2() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описан журнал2() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 41e9a1457f97fa04a4daa54e1929f27d8a448ae3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513130"
---
# <a name="log2"></a>log2()

`log2()`возвращает функцию базы-2 logarithm.  

**Синтаксис**

`log2(`*X*`)`

**Аргументы**

* *x*: Реальное число > 0.

**Возвращает**

* Logarithm будет основанием-2 logarithm: обратная экспоненциальной функции (exp) с основанием 2.
* `null`если аргумент отрицательный или нулевый или `real` не может быть преобразован в значение. 

**См. также:**

* Для естественных (базовых) логарифмов [см.](log-function.md)
* Для общих (базовых-10) логарифмов см. [log10()](log10-function.md).