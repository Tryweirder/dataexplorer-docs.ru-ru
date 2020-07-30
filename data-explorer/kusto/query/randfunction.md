---
title: Rand () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается функция RAND () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53fc7512c1a0fb2019526f48ade54fabbd351d05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345935"
---
# <a name="rand"></a>rand()

Возвращает случайное число.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>Синтаксис

* `rand()`— Возвращает значение типа `real` с равномерным распределением в диапазоне [0,0, 1,0).
* `rand(`*N* `)` — возвращает значение типа, `real` выбранное с равномерным распределением из набора {0,0, 1,0,..., *N* -1}.