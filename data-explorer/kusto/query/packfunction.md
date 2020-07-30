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
ms.openlocfilehash: 560f7ca9f423eb57fd9be0478e0e51dc2ce26755
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346513"
---
# <a name="pack"></a>pack()

Создает `dynamic` объект (контейнер свойств) из списка имен и значений.

Псевдоним для `pack_dictionary()` функции.

## <a name="syntax"></a>Синтаксис

`pack(`*Key1* `,` *Значение1* `,` *key2* `,` *Значение2*`,... )`

## <a name="arguments"></a>Аргументы

* Чередующийся список ключей и значений (Общая длина списка должна быть четной)
* Все ключи должны быть непустыми константными строками

## <a name="examples"></a>Примеры

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
