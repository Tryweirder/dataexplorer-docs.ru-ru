---
title: миграция () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается миграция () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: 5d186172a0be1780347dbf89600c200c00687291
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252027"
---
# <a name="translate"></a>translate()

Заменяет набор символов ("Сеарчлист") другим набором символов ("Реплацементлист") в заданной строке.
Функция ищет символы в "Сеарчлист" и заменяет их соответствующими символами в "Реплацементлист"

## <a name="syntax"></a>Синтаксис

`translate(`*сеарчлист* `,` *реплацементлист* `,` *текстовая надпись*`)`

## <a name="arguments"></a>Аргументы

* *сеарчлист*: список символов, которые следует заменить
* *реплацементлист*: список символов, которые должны заменить символы в "сеарчлист"
* *текст*: строка для поиска

## <a name="returns"></a>Результаты

*текст* после замены всех окурренцес символов в "реплацементлист" соответствующими символами в "сеарчлист"

## <a name="examples"></a>Примеры

|Входные данные                                 |Выходные данные   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|