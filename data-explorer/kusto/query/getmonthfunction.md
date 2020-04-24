---
title: getmonth() - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается getmonth() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: b0224d8ea9c99ce72604a5b7df248394bc6317fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514422"
---
# <a name="getmonth"></a>getmonth()

Возвращает номер месяца (1-12) для аргумента типа datetime.

Другой псевдоним: monthoyear()

**Пример**

```kusto
print month = getmonth(datetime(2015-10-12))
```