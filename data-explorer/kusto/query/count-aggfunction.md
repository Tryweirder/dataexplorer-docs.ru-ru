---
title: подсчет () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается количество () (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 59b898d44507d844db1f714ef15effa004e5546f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517006"
---
# <a name="count-aggregation-function"></a>кол() (функция агрегирования)

Возвращает подсчет записей на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)
* Используйте функцию агрегации [countif](countif-aggfunction.md) для подсчета только `true`записей, для которых некоторые предикатные возвраты.

**Синтаксис**

Суммировать`count()`

**Возвращает**

Возвращает подсчет записей на группу обобщений (или в общей сложности, если обобщение осуществляется без группировки).