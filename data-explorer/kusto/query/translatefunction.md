---
title: перевод () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается перевод () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: f128ce31af7fc720ee81b7471d3d74616197b8d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505718"
---
# <a name="translate"></a>translate()

Заменяет набор символов ('searchList') на другой набор символов ('replacementList') в данной строке.
Функция ищет символы в 'searchList' и заменяет их соответствующими символами в 'replacementList'

**Синтаксис**

`translate(`*SearchList* `,` *replacementList* `,` *текст*`)`

**Аргументы**

* *searchList*: Список символов, которые должны быть заменены
* *replacementList*: Список символов, которые должны заменить символы в 'searchList'
* *текст*: Строка для поиска

**Возвращает**

*текст* после замены всех ocurrences символов в 'replacementList' с соответствующими символами в 'searchList'

**Примеры**

|Входные данные                                 |Выходные данные   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|