---
title: log10() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описан журнал10() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 9ccc83ff466d0414f793b7cfbbcf10d2ca169348
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513198"
---
# <a name="log10"></a>log10()

`log10()`возвращает общую (базовую-10) функцию logarithm.  

**Синтаксис**

`log10(`*X*`)`

**Аргументы**

* *x*: Реальное число > 0.

**Возвращает**

* Общим logarithm будет основанием-10 logarithm: обратная экспоненциальной функции (exp) с основанием 10.
* `null`если аргумент отрицательный или нулевый или `real` не может быть преобразован в значение. 

**См. также:**

* Для естественных (базовых) логарифмов [см.](log-function.md)
* Для баз-2 логарифмов [см. log2()](log2-function.md)