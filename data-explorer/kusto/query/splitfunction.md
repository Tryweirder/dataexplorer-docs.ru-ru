---
title: Split () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается разбиение () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: efd3812086631451b77ca1edd846ec9bd75990fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351018"
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