---
title: Политика кэша (горячий и холодный кэш) — Azure обозреватель данных
description: В этой статье описывается политика кэширования (горячий и холодный кэш) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: af224c630cb835d190b8fd6655a6d42f7fd7fee9
ms.sourcegitcommit: d79d3aa9aaa70cd23e3107ef12296159322e1eb5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86475615"
---
# <a name="cache-policy-hot-and-cold-cache"></a>Политика кэширования ("горячее" и "холодное" кэширование) 

Azure обозреватель данных сохраняет полученные данные в надежном хранилище (чаще всего это хранилище BLOB-объектов Azure), от фактической обработки (например, на вычислительных узлах Azure). Чтобы ускорить выполнение запросов к этим данным, Azure обозреватель данных кэширует их или его части, на узлах обработки, SSD или даже в ОЗУ. Обозреватель данных Azure включает сложный механизм кэширования, предназначенный для интеллектуальной выборки объектов данных для кэширования. Кэш позволяет обозреватель данныху Azure описывать используемые артефакты данных, чтобы более важные данные могли иметь приоритет. Например, индексы столбцов и сегменты данных столбцов,

Оптимальная производительность запросов достигается при кэшировании всех полученных данных. Иногда некоторые данные не выключают затраты на поддержание "теплого" в локальном хранилище SSD.
Например, многие команды считают, что редко используемые более старые записи журнала менее важны.
Они предпочитают снизить производительность при запросе этих данных, а не платить, чтобы не усложнять все время.

Кэш обозреватель данных для Azure предоставляет детальную **политику кэширования** , которую клиенты могут использовать для различения: **горячего** и **холодного кэша данных**. Кэш обозреватель данных Azure пытается удержать все данные, попадающие в категорию критического кэша, на локальном SSD (или ОЗУ) вплоть до определенного размера кэша горячих данных. Оставшееся локальное пространство SSD будет использоваться для хранения данных, не классифицированных как горячий. Одно из полезных признаков этой схемы заключается в том, что запросы, которые загружают большое количество холодных данных из надежного хранилища, не будут исключать данные из кэша горячего хранения данных. В результате этого не будет существенного влияния на запросы, включающие данные в критический кэш данных.

Ниже перечислены основные последствия настройки политики горячего кэша.
* **Стоимость**. стоимость надежного хранилища может быть значительно ниже, чем для локального SSD. Сейчас в Azure 45 раз дешевле.
* **Производительность**. запросы к данным выполняются быстрее, если они находятся на локальном SSD, особенно для запросов диапазона, которые просматривают большие объемы данных.  

Используйте [команду политики кэша](cache-policy.md) для управления политикой кэширования.

> [!TIP]
>Azure обозреватель данных предназначен для нерегламентированных запросов с промежуточными результирующими наборами, подпользующих общий объем ОЗУ кластера.
>При работе с большими заданиями, например с картой-reduce, где вы хотите хранить промежуточные результаты в постоянном хранилище, таком как SSD, используйте функцию непрерывного экспорта. Эта функция позволяет выполнять долго выполняемые пакетные запросы с помощью таких служб, как HDInsight или Azure Databricks.
 
## <a name="how-cache-policy-is-applied"></a>Как применяется политика кэширования

При приеме данных в Azure обозреватель данных система отслеживает дату и время приема, а также созданную область. Значение даты и времени приема экстента (или максимальное значение, если экстент был построен из нескольких уже существующих экстентов), используется для вычисления политики кэширования.

> [!NOTE]
> Значение для даты приема и времени можно указать с помощью свойства приема `creationTime` .

По умолчанию действующая политика имеет значение `null` , то есть все данные считаются **горячими**.
`null`Политика уровня базы данных переопределяет политику, отличную от уровня таблицы.

## <a name="scoping-queries-to-hot-cache"></a>Определение области запросов для оперативного кэша

Kusto поддерживает запросы, которые находятся в области только для данных с активным кэшем.

> [!NOTE]
> Области данных применяются только к сущностям, поддерживающим политики кэширования, например таблицы. Он игнорируется для других сущностей, таких как внешние таблицы.

Существует несколько возможностей запроса.
* Добавьте в запрос свойство запроса клиента `query_datascope` с именем.
   Возможные значения: `default` , `all` и `hotcache` .
* Используйте `set` инструкцию в тексте запроса: `set query_datascope='...'` .
   Возможные значения: для свойства запроса клиента.
* Добавьте `datascope=...` текст сразу после ссылки на таблицу в тексте запроса. 
   Возможные значения: `all` и `hotcache`.

`default`Значение указывает на использование параметров кластера по умолчанию, которые определяют, что запрос должен охватывать все данные.

При обнаружении несоответствия между различными методами `set` приоритет имеет преимущество над свойством запроса клиента. Указание значения для ссылки на таблицу имеет приоритет над обоими.

Например, в следующем запросе все ссылки на таблицы будут использовать только данные оперативного кэша, за исключением второй ссылки на T, которая ограничивается всеми данными:

```kusto
set query_datascope="hotcache";
T | union U | join (T datascope=all | where Timestamp < ago(365d) on X
```

## <a name="cache-policy-vs-retention-policy"></a>Политика кэширования и политика хранения

Политика кэша не зависит от [политики хранения](./retentionpolicy.md): 
- Политика кэширования определяет способ определения приоритетов ресурсов. Запросы к важным данным будут более быстрыми и устойчивы к влиянию запросов на менее важные данные.
- Политика хранения определяет экстент запрашиваемых данных в таблице или базе данных (в частности, `SoftDeletePeriod` ).

Настройте эту политику для достижения оптимального баланса между затратами и производительностью в зависимости от ожидаемого шаблона запроса.

Пример
* `SoftDeletePeriod`= 56d
* `hot cache policy`= 28D

В этом примере данные за последние 28 дней будут находиться на SSD кластера, а дополнительные данные за 28 дней будут храниться в хранилище BLOB-объектов Azure.
Можно выполнять запросы к данным за полный 56 дней.
 
