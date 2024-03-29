---
title: Replace () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается замена () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d9fdcfaad41201cd99796c3f4966593aa7480e49
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243059"
---
# <a name="replace"></a>replace()

Заменяет все найденные совпадения регулярного выражения другой строкой.

## <a name="syntax"></a>Синтаксис

`replace(`*регулярное выражение* `,` *переписать* `,` *текстовая надпись*`)`

## <a name="arguments"></a>Аргументы

* *Regex*: [регулярное выражение](https://github.com/google/re2/wiki/Syntax) для поиска *текста*. Оно может содержать группы захвата в '('круглых скобках')'. 
* *переписать*: регулярное выражение замены для любых совпадений, выполненных *матчингрежекс*. Используйте `\0` для указания полного соответствия, `\1` для первой группы записи, `\2` и так далее — для следующих групп записи.
* *Text*: строка.

## <a name="returns"></a>Результаты

*text* после замены всех вхождений *regex* результатами вычислений *rewrite*. Совпадения не перекрываются.

## <a name="example"></a>Пример

Выражение:

```kusto
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Получены следующие результаты:

| x    | str | заменена|
|---|---|---|
| 1    | Число равно: 1,000000  | Число было равно: 1,000000|
| 2    | Число равно: 2,000000  | Число было равно: 2,000000|
| 3    | Число равно: 3,000000  | Число было равно: 3,000000|
| 4    | Число равно: 4,000000  | Число было равно: 4,000000|
| 5    | Число равно: 5,000000  | Число было равно: 5,000000|
 