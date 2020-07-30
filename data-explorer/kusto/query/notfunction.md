---
title: not () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается not () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fed2a55c8fa1c7689c087ccdeaa64ff576bea401
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346598"
---
# <a name="not"></a>not()

Изменяет значение аргумента на противоположное `bool` .

```kusto
not(false) == true
```

## <a name="syntax"></a>Синтаксис

`not(`*выражения*`)`

## <a name="arguments"></a>Аргументы

* *expr*: `bool` выражение, которое должно быть отменено.

## <a name="returns"></a>Результаты

Возвращает Инвертированное логическое значение `bool` аргумента.