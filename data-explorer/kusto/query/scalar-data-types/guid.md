---
title: Тип данных guid - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается тип данных Guid в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 382b2da0ab791cf3e2fea0e1c785ee42634aab07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509866"
---
# <a name="the-guid-data-type"></a>Тип данных guid

Тип `guid` `uuid`данных (, `uniqueid`) представляет собой 128-битное глобально уникальное значение.

> [!WARNING]
> На момент написания этой статьи поддержка типа `guid` является неполной.
> Основным пробелом является отсутствие индекса на столбцах этого типа, влияющих на производительность запросов, которые предпосылели этот тип.
> Мы настоятельно рекомендуем разработчикам использовать вместо него значения с типом `string`.

## <a name="guid-literals"></a>гид буквальные

Чтобы представить буквальный `guid`тип, используйте следующий формат:

```kusto
guid(74be27de-1e4e-49d9-b579-fe0b331d3642)
```

Специальная `guid(null)` форма представляет [нулевую стоимость.](null-values.md)