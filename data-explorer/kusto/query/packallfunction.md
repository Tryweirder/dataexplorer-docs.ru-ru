---
title: pack_all () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны pack_all () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3c6a22b656e28b8b7113864e0b3f9636a4fb364d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511940"
---
# <a name="pack_all"></a>pack_all()

Создает `dynamic` объект (мешок свойства) из всех столбцов табликов.

**Синтаксис**

`pack_all()`

**Примеры**

С учетом таблицы SmsMessages 

|ИсточникНомер |TargetNumber| ЧарсКтон
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

этот запрос
```kusto
SmsMessages | extend Packed=pack_all()
``` 

Возвращает:

|TableName |ИсточникНомер |TargetNumber | Упакованные
|---|---|---|---
|SmsСообщения|555-555-1234 |555-555-1212 | "ИсточникНомер":"555-555-1234", "TargetNumber":"555-555-1212", "CharsCount": 46
|SmsСообщения|555-555-1234 |555-555-1213 | "ИсточникНомер":"555-555-1234", "TargetNumber":"555-555-1213", "CharsCount": 50
|SmsСообщения|555-555-1212 |555-555-1234 | "ИсточникНомер":"555-555-1212", "TargetNumber":"555-555-1234", "CharsCount": 32