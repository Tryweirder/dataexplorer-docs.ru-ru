---
title: Pack () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Pack () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94629ae8f4795e28cbfb0c41f06596731cdd8d9
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717331"
---
# <a name="pack"></a>pack()

Создает `dynamic` объект (контейнер свойств) из списка имен и значений.

Псевдоним для `pack_dictionary()` функции.

**Синтаксис**

`pack(`*Key1* `,` *Значение1* `,` *key2* `,` *Значение2*`,... )`

**Аргументы**

* Чередующийся список ключей и значений (Общая длина списка должна быть четной)
* Все ключи должны быть непустыми константными строками

**Примеры**

В следующем примере возвращается `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`.

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

Позволяет использовать 2 таблицы, Смсмессажес и Ммсмессажес:

Смсмессажес таблицы 

|саурценумбер |таржетнумбер| чарскаунт
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Ммсмессажес таблицы 

|саурценумбер |таржетнумбер| аттачментсизе | AttachmentType | аттачментнаме
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | jpeg | Pic1
|555-555-1234 |555-555-1212 | 250 | jpeg | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

этот запрос
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmentSize", AttachmentSize, "AttachmentType", AttachmentType, "AttachmentName", AttachmentName))
| where SourceNumber == "555-555-1234"
``` 

Возвращает:

|TableName |саурценумбер |таржетнумбер | Распаковывается
|---|---|---|---
|смсмессажес|555-555-1234 |555-555-1212 | {"Чарскаунт": 46}
|смсмессажес|555-555-1234 |555-555-1213 | {"Чарскаунт": 50}
|ммсмессажес|555-555-1234 |555-555-1212 | {"Аттачментсизе": 250, "AttachmentType": "JPEG", "Аттачментнаме": "Pic2"}
|ммсмессажес|555-555-1234 |555-555-1213 | {"Аттачментсизе": 300, "AttachmentType": "PNG", "Аттачментнаме": "Pic3"}
