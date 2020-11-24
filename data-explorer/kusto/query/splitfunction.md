---
title: Split () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается разбиение () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 4baae5bee8dd1e85a304be7fb4eae988acc404d8
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512950"
---
# <a name="split"></a>split()

Разделяет заданную строку в соответствии с заданным разделителем и возвращает массив строк с вложенными строками.

При необходимости может быть возвращена конкретная подстрока, если она существует.

```kusto
split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Синтаксис

`split(`*Исходный код* `,` *Разделитель* [ `,` *рекуестединдекс*]`)`

## <a name="arguments"></a>Аргументы

* *Source*: исходная строка, которая будет разделяться в соответствии с заданным разделителем.
* *delimiter*— разделитель, который будет использоваться для разделения исходной строки.
* *requestedIndex* — необязательный индекс типа `int`, начинающийся с нуля. Если указан, возвращаемый массив строк будет содержать запрошенную подстроку, если она существует. 

## <a name="returns"></a>Возвращаемое значение

Массив строк, содержащий подстроки заданной исходной строки, разделенные указанным разделителем.

## <a name="examples"></a>Примеры

```kusto
print
    split("aa_bb", "_"),           // ["aa","bb"]
    split("aaa_bbb_ccc", "_", 1),  // ["bbb"]
    split("", "_"),                // [""]
    split("a__b", "_"),            // ["a","","b"]
    split("aabbcc", "bb")          // ["aa","cc"]
```