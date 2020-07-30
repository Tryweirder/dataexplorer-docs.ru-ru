---
title: миграция () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается миграция () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: d0e99048f3f1b0e3ce5c6c59a65ea645b22d15fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340060"
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

## <a name="returns"></a>Возвращаемое значение

*текст* после замены всех окурренцес символов в "реплацементлист" соответствующими символами в "сеарчлист"

## <a name="examples"></a>Примеры

|Входные данные                                 |Выходные данные   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|