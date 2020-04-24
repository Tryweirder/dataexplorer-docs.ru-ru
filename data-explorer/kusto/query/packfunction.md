---
title: пакет () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается пакет () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7b43be96f272ab929434f10cac910bd4072e650
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511838"
---
# <a name="pack"></a>pack()

Создает `dynamic` объект (мешок собственности) из списка имен и значений.

Прозвище `pack_dictionary()` для функционирования.

**Синтаксис**

`pack(`*key1* `,` *value1* `,` *key2* `,` *value2*`,... )`

**Аргументы**

* Переменный список ключей и значений (общая длина списка должна быть ровной)
* Все клавиши должны быть непустыми постоянными строками

**Примеры**

В следующем примере возвращается `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`.

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

Позволяет взять 2 таблицы, SmsСообщения и MmsMessages:

Таблица SmsСообщения 

|ИсточникНомер |TargetNumber| ЧарсКтон
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Таблица MmsСообщения 

|ИсточникНомер |TargetNumber| АттакмнетРазмер | АттакмнетТип | АттакмнетНамили
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | JPEG | Pic1
|555-555-1234 |555-555-1212 | 250 | JPEG | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

этот запрос
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmnetSize", AttachmnetSize, "AttachmnetType", AttachmnetType, "AttachmnetName", AttachmnetName))
| where SourceNumber == "555-555-1234"
``` 

Возвращает:

|TableName |ИсточникНомер |TargetNumber | Упакованные
|---|---|---|---
|SmsСообщения|555-555-1234 |555-555-1212 | "ЧарсКТон": 46"
|SmsСообщения|555-555-1234 |555-555-1213 | "ЧарсКТон": 50
|MmsСообщения|555-555-1234 |555-555-1212 | "AttachmnetSize": 250, "AttachmnetType": "jpeg", "AttachmnetName": "Pic2"
|MmsСообщения|555-555-1234 |555-555-1213 | "AttachmnetSize": 300, "AttachmnetType": "png", "AttachmnetName": "Pic3"