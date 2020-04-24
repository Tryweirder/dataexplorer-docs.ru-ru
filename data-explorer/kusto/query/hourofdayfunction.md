---
title: часовой день () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается часовое время () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 08fdb45af5eec7f71d491725ea58a7d72c06371b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514082"
---
# <a name="hourofday"></a>hourofday()

Возвращает целый номер, представляющий часовой номер данной даты

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

**Синтаксис**

`hourofday(`*a_date*`)`

**Аргументы**

* `a_date` — значение в формате `datetime`.

**Возвращает**

`hour number`дня (0-23).