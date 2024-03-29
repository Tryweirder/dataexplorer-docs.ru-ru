---
title: . Отображение экстентов — Azure обозреватель данных
description: В этой статье описывается команда "показывать экстенты" в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: c0e2ffa76becc747b03b907dfe8a356e4c1a49a3
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060706"
---
# <a name="show-extents"></a>. Отображение экстентов

Показывает экстенты из указанной базы данных или таблицы.

> [!NOTE]
> Сегменты данных называются **экстентами** в Kusto, а все команды используют "экстент" или "экстенты" в качестве синонима.
> Дополнительные сведения о экстентах см. в статье [Общие сведения о экстентах (сегментах данных)](extents-overview.md).

## <a name="cluster-level"></a>Уровень кластера

`.show` `cluster` `extents` [`hot`]

Отображает сведения о экстентах (сегментах данных), имеющихся в кластере.
Если `hot` указан параметр, показывает только те экстенты, которые должны находиться в активном кэше.

## <a name="database-level"></a>На уровне базы данных

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,` *Екстентидн* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

Отображает сведения об экстентах (сегментах данных), которые имеются в указанной базе данных.
Если `hot` указан параметр, показывает только те экстенты, которые должны находиться в активном кэше.

## <a name="table-level"></a>Уровень таблицы

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,` *Екстентидн* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,` *Табленамен* `)` `extents`[ `(` *ExtentId1* `,` ... `,` *Екстентидн* `)` ] [ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

Отображает сведения об экстентах (сегментах данных), которые имеются в указанных таблицах. База данных берется из контекста команды.
Если `hot` указан параметр, отображаются только те экстенты, которые должны находиться в активном кэше.

## <a name="notes"></a>Примечания

* Использование команд базы данных или уровня таблицы выполняется гораздо быстрее, чем фильтрация (добавление `| where DatabaseName == '...' and TableName == '...'` ) результатов команды уровня кластера.
* Если указан необязательный список идентификаторов экстентов, то возвращенный набор данных ограничен только этими экстентами.
    * Этот метод выполняется гораздо быстрее, чем фильтрация (добавление `| where ExtentId in(...)` в) результатов выполнения команд ««исходное»».
* Если `tags` указаны фильтры:
    * Возвращаемый список ограничен этими экстентами, Коллекция тегов которых подчиняется *всем* указанным фильтрам тегов.
    * Этот метод выполняется гораздо быстрее, чем фильтрация (добавление `| where Tags has '...' and Tags contains '...'` в) результатов выполнения команд ««исходное»».
    * `has`Фильтры — это фильтры равенства. Будут отфильтрованы экстенты, не помеченные ни одним из указанных тегов.
    * `!has`Фильтры являются отрицательными фильтрами равенства. Экстенты, помеченные любым из указанных тегов, будут отфильтрованы.
    * `contains`фильтры не учитывают регистр в фильтрах подстрок. Экстенты, для которых не заданы строки в качестве подстроки любого из их тегов, будут отфильтрованы.
    * `!contains`Фильтры являются отрицательными фильтрами подстрок без учета регистра. Экстенты, которые содержат указанные строки в виде подстроки любого из их тегов, будут отфильтрованы.
  
## <a name="output-parameters"></a>Параметры вывода

|Выходной параметр |Type |Описание: |
|---|---|---|
|екстентид |Guid |Идентификатор экстента 
|имя_базы_данных |Строка |База данных, к которой принадлежит экстент
|TableName |Строка |Таблица, к которой относятся экстенты
|макскреатедон |Дата и время |Дата и время создания экстента. Для Объединенного экстента максимальное время создания между исходными экстентами
|оригиналсизе |Double |Исходный размер данных экстента в байтах
|екстентсизе |Double |Размер экстента в памяти (сжатый и индексный)
|CompressedSize |Double |Сжатый размер данных экстента в памяти
|IndexSize |Double |Размер индекса данных экстента
|Blocks |Long |Число блоков данных в экстенте
|Сегменты |Long |Число сегментов данных в экстенте
|ассигнеддатанодес |Строка | Не рекомендуется (пустая строка)
|лоадеддатанодес |Строка |Не рекомендуется (пустая строка)
|екстентконтаинерид |Строка | Идентификатор контейнера экстента, в котором находится экстент
|RowCount |Long |Число строк в экстенте
|минкреатедон |Дата и время |Дата и время создания экстента. Для Объединенного экстента — минимальное время создания между исходными экстентами.
|Tags|Строка|Теги (если имеются), определенные для экстента
 
## <a name="examples"></a>Примеры

### <a name="tagged-extent"></a>Область с тегами

Экстент `E` в таблице `T` помечен тегами `aaa` , `BBB` и `ccc` .

* Этот запрос возвратит `E` :
    
   ```kusto
    .show table T extents where tags has 'aaa' and tags contains 'bb'
   ```
   
* Этот запрос не будет возвращен `E` , так как он не помечен как `aa` :
    
   ```kusto
    .show table T extents where tags has 'aa' and tags contains 'bb'
   ```
    
* Этот запрос возвратит `E` :
    
   ```kusto
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
   ```

### <a name="show-volume-of-extents-created"></a>Отображение созданных объемов экстентов

Отображение объема создаваемых в час экстентов в определенной базе данных

```kusto 
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  
```

### <a name="show-volume-of-data-arriving-by-table-per-hour"></a>Отображение объема данных, поступающих по таблице за час

```kusto 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart
```

### <a name="show-data-size-distribution-by-table"></a>Показывать распределение размера данных по таблице

```kusto 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName
```

### <a name="show-all-extents-in-the-database-named-gamesdb"></a>Отобразить все экстенты в базе данных с именем "Гамесдб"

```kusto 
.show database GamesDB extents
```

### <a name="show-all-extents-in-the-table-named-games"></a>Отобразить все экстенты в таблице с именем games

```kusto 
.show table Games extents
```

### <a name="show-all-extents-in-specific-tables"></a>Отображение всех экстентов в конкретных таблицах

Отобразить все экстенты в таблицах с именами "TaggingGames1" и "TaggingGames2" с тегами "TAG1" и "tag2"

```kusto 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
```
