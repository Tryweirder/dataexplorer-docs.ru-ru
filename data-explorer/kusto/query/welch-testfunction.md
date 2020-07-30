---
title: welch_test () — обозреватель данных Azure
description: В этой статье описывается welch_test () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cc0cc7ca77efeae583adeaf7a983d18498e5af96
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338275"
---
# <a name="welch_test"></a>welch_test()

Вычисление p_value [функции Уэлч-Test](https://en.wikipedia.org/wiki/Welch%27s_t-test)

```kusto
// s1, s2 values are from https://en.wikipedia.org/wiki/Welch%27s_t-test
print
    s1 = dynamic([27.5, 21.0, 19.0, 23.6, 17.0, 17.9, 16.9, 20.1, 21.9, 22.6, 23.1, 19.6, 19.0, 21.7, 21.4]),
    s2 = dynamic([27.1, 22.0, 20.8, 23.4, 23.4, 23.5, 25.8, 22.0, 24.8, 20.2, 21.9, 22.1, 22.9, 20.5, 24.4])
| mv-expand s1 to typeof(double), s2 to typeof(double)
| summarize m1=avg(s1), v1=variance(s1), c1=count(), m2=avg(s2), v2=variance(s2), c2=count()
| extend pValue=welch_test(m1,v1,c1,m2,v2,c2)

// pValue = 0.021
```

## <a name="syntax"></a>Синтаксис

`welch_test(`*mean1* `, ` *variance1* `, ` *count1* `, ` *mean2* `, ` *variance2* `, ` *count2*`)`

## <a name="arguments"></a>Аргументы

* *mean1*: выражение, представляющее среднее значение (среднее) первого ряда
* *variance1*: выражение, представляющее значение дисперсии для первого ряда
* *count1*: выражение, представляющее количество значений в первом ряде
* *mean2*: выражение, представляющее среднее значение второго ряда.
* *variance2*: выражение, представляющее значение дисперсии второго ряда
* *count2*: выражение, представляющее количество значений во втором ряде

## <a name="returns"></a>Результаты

Из [Википедии](https://en.wikipedia.org/wiki/Welch%27s_t-test):

В статистике t-тест Уэлч — это двухвыборочный тест расположения, который используется для проверки гипотезы о том, что две Генеральной совокупности имеют равные значения. T-тест Уэлч — это адаптация t-теста учащегося, которая более надежна, если два образца имеют неравные дисперсии и размеры выборки не равны. Эти тесты часто называются t-тестами с непарными или независимыми примерами. Тесты обычно применяются, когда статистические единицы, лежащие в основе сравниваемых примеров, не перекрываются. T-тест Уэлч менее популярен, чем t-test, и может быть менее знаком читателям. Тест также называется "неравенство Уэлч неравенства" t-test "или" неравные дисперсии t-test ".
