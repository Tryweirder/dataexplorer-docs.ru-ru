---
title: подключаемый модуль ipv4_lookup — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль ipv4_lookup в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: ec826e7d16e575fa4904aba93575ab7e605d2b18
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511158"
---
# <a name="ipv4_lookup-plugin"></a>подключаемый модуль ipv4_lookup

`ipv4_lookup`Подключаемый модуль ищет значение IPv4 в таблице уточняющих запросов и возвращает строки с совпадающими значениями.

```kusto
T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey)

T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey, return_unmatched = true)
```

## <a name="syntax"></a>Синтаксис

*T* `|` `evaluate` `ipv4_lookup(` *лукуптабле* `,` *SourceIPv4Key* `,` *LookupKey* [ `,` *return_unmatched* ] `)`

## <a name="arguments"></a>Аргументы

* *T*: табличный ввод, столбец *SourceIPv4Key* которого будет использоваться для сопоставления IPv4.
* *Лукуптабле*: табличное или табличное выражение с данными подстановки IPv4, столбец *LookupKey* которого будет использоваться для сопоставления IPv4. Значения IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).
* *SourceIPv4Key*: столбец *T* с строкой IPv4 для поиска в *лукуптабле*. Значения IPv4 можно маскировать с помощью [нотации префикса IP](#ip-prefix-notation).
* *LookupKey*: столбец *лукуптабле* с строкой IPv4, соответствующий каждому значению *SourceIPv4Key* .
* *return_unmatched*: логический флаг, определяющий, должен ли результат включать все или только совпадающие строки (по умолчанию: `false` -только совпадающие строки).

### <a name="ip-prefix-notation"></a>IP-префиксная нотация
 
IP-адреса можно определять с `IP-prefix notation` помощью символа косой черты ( `/` ).
IP-адрес, расположенный слева от косой черты ( `/` ), является базовым IP-адресом. Число (от 1 до 32) справа от косой черты ( `/` ) — это число непрерывных 1 бита в маске. 

Например, в 192.168.2.0/24 будет сопоставлена сеть/маска подсети, содержащая 24 смежных бита или 255.255.255.0 в точечно-десятичном формате.

## <a name="returns"></a>Возвращаемое значение

`ipv4_lookup`Подключаемый модуль возвращает результат JOIN (Уточняющий запрос), основанный на ключе IPv4. Схема таблицы — это объединение исходной таблицы и таблицы уточняющих запросов аналогично результату [ `lookup` оператора](lookupoperator.md).

Если аргумент *return_unmatched* имеет значение `true` , то результирующая таблица будет содержать как совпадающие, так и несовпадающие строки (заполненные значениями NULL).

Если аргумент *return_unmatched* имеет значение `false` или опущен (используется значение по умолчанию `false` ), результирующая таблица будет содержать столько записей, сколько соответствует результатам сопоставления. Этот вариант поиска обеспечивает лучшую производительность по сравнению с `return_unmatched=true` выполнением.

> [!NOTE]
> * Этот подключаемый модуль охватывает сценарий подключения на основе IPv4, при котором предполагается небольшой размер таблицы подстановки (100 КБ/200 000 деятелей строк), при этом размер входной таблицы может быть больше.
> * Производительность подключаемого модуля зависит от размеров таблиц подстановки и источника данных, количества столбцов и числа совпадающих записей.

## <a name="examples"></a>Примеры

### <a name="ipv4-lookup---matching-rows-only"></a>Просмотр IPv4 — сопоставление только строк

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string, continent_code:string ,continent_name:string, country_iso_code:string, country_name:string)
[
  "111.68.128.0/17","AS","Asia","JP","Japan",
  "5.8.0.0/19","EU","Europe","RU","Russia",
  "223.255.254.0/24","AS","Asia","SG","Singapore",
  "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
  "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
  '2.20.183.12',   // United Kingdom
  '5.8.1.2',       // Russia
  '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network)
```

|см|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|Европа|ГБ|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|Европа|RU|Россия|

### <a name="ipv4-lookup---return-both-matching-and-non-matching-rows"></a>Просмотр IPv4 — возврат совпадающих и несовпадающих строк

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: 
// https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string,continent_code:string ,continent_name:string ,country_iso_code:string ,country_name:string )
[
    "111.68.128.0/17","AS","Asia","JP","Japan",
    "5.8.0.0/19","EU","Europe","RU","Russia",
    "223.255.254.0/24","AS","Asia","SG","Singapore",
    "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
    "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|см|network|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|Европа|ГБ|United Kingdom|
|5.8.1.2|5.8.0.0/19|EU|Европа|RU|Россия|
|192.165.12.17||||||

### <a name="ipv4-lookup---using-source-in-external_data"></a>Поиск IPv4 — использование источника в external_data ()

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let IP_Data = external_data(network:string,geoname_id:long,continent_code:string,continent_name:string ,country_iso_code:string,country_name:string,is_anonymous_proxy:bool,is_satellite_provider:bool)
    ['https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv'];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Sweden
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|см|network|geoname_id|continent_code|continent_name|country_iso_code|country_name|is_anonymous_proxy|is_satellite_provider|
|---|---|---|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|2635167|EU|Европа|ГБ|United Kingdom|0|0|
|5.8.1.2|5.8.0.0/19|2017370|EU|Европа|RU|Россия|0|0|
|192.165.12.17|192.165.8.0/21|2661886|EU|Европа|SE|Швеция|0|0|
