---
title: Rand () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается функция RAND () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1206f72b3951601c105de450bc6923861ad011ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241281"
---
# <a name="rand"></a>rand()

Возвращает случайное число.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>Синтаксис

* `rand()` — Возвращает значение типа `real` с равномерным распределением в диапазоне [0,0, 1,0).
* `rand(`*N* `)` — возвращает значение типа, `real` выбранное с равномерным распределением из набора {0,0, 1,0,..., *N* -1}.