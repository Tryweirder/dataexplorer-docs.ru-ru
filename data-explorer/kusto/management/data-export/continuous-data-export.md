---
title: Непрерывная экспорт данных в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается непрерывный экспорт данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/27/2020
ms.openlocfilehash: 7abcead19e0306853bc6a585a41b5b79657a6842
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617720"
---
# <a name="continuous-data-export"></a>Экспорт непрерывных данных

Непрерывно экспортируйте данные из Kusto во [внешнюю таблицу](../externaltables.md). Внешняя таблица определяет назначение (например, хранилище BLOB-объектов Azure) и схему экспортированных данных. Экспортированные данные определяются с помощью периодического выполнения запроса. Результаты хранятся во внешней таблице. Этот процесс гарантирует, что все записи экспортируются «ровно раз» (за исключением таблиц измерений, в которых все записи оцениваются во всех выполнениях). 

При экспорте непрерывных данных необходимо [создать внешнюю таблицу](../externaltables.md#create-or-alter-external-table) , а затем [создать определение непрерывного экспорта](#create-or-alter-continuous-export) , указывающее на внешнюю таблицу. 

> [!NOTE] 
> * Kusto не поддерживает экспорт исторических записей, полученных до создания непрерывного экспорта (в рамках непрерывного экспорта). Исторические записи можно экспортировать отдельно с помощью команды (не непрерывной) [экспорта](export-data-to-an-external-table.md). Дополнительные сведения см. в разделе [Экспорт исторических данных](#exporting-historical-data). 
> * Непрерывный экспорт не работает для данных, полученных с помощью приема потоковой передачи. 
> * В настоящее время непрерывный экспорт нельзя настроить для таблицы, в которой включена [политика безопасность на уровне строк](../../management/rowlevelsecuritypolicy.md) .
> * Непрерывный экспорт не поддерживается для внешних таблиц с `impersonate` в [строках подключения](../../api/connection-strings/storage.md).
 
## <a name="notes"></a>Примечания

* Гарантия экспорта «только один раз» предназначена *только* для файлов, передаваемых в команде « [Показывать экспортированные артефакты](#show-continuous-export-exported-artifacts)». 
Непрерывный экспорт *не гарантирует,* что каждая запись будет записываться только один раз во внешнюю таблицу. Если сбой происходит после начала экспорта и некоторые артефакты уже записаны во внешнюю таблицу, то внешняя таблица _может_ содержать дубликаты (или даже поврежденные файлы, если операция записи была прервана до завершения). В таких случаях артефакты не удаляются из внешней таблицы, но *не* будут отображаться в [команде показывать экспортированные артефакты](#show-continuous-export-exported-artifacts). Использование экспортированных файлов с помощью `show exported artifacts command` гарантирует отсутствие дубликатов (а не повреждений).
* Чтобы гарантировать Однократный экспорт, непрерывный экспорт использует [курсоры базы данных](../databasecursor.md). 
Поэтому необходимо включить [политику инжестионтиме](../ingestiontime-policy.md) для всех таблиц, на которые имеются ссылки в запросе, которые должны обрабатываться «ровно раз» в процессе экспорта. Политика включена по умолчанию во всех вновь создаваемых таблицах.
* Схема вывода запроса экспорта *должна* соответствовать схеме внешней таблицы, в которую экспортируется. 
* Непрерывный экспорт не поддерживает вызовы между базами данных и кластерами.
* Непрерывный экспорт выполняется в соответствии с настроенным для него периодом времени. Рекомендуемое значение для этого интервала составляет по крайней мере несколько минут в зависимости от задержек, которые вы хотите принять. Непрерывный экспорт *не* предназначен для непрерывной потоковой передачи данных из Kusto. Он выполняется в распределенном режиме, где все узлы экспортируются параллельно. Если диапазон данных, запрашиваемых каждым запуском, невелик, выходные данные непрерывного экспорта будут иметь много мелких артефактов (число зависит от числа узлов в кластере). 
* Количество операций экспорта, которые могут выполняться одновременно, ограничено емкостью экспорта данных кластера (см. раздел [регулирование](../../management/capacitypolicy.md#throttling)). Если у кластера недостаточно емкости для выполнения всех непрерывных операций экспорта, некоторые из них начнут задержкой. 
 
* По умолчанию все таблицы, упоминаемые в запросе экспорта, считаются [таблицами фактов](../../concepts/fact-and-dimension-tables.md). 
Поэтому они *ограничены* курсором базы данных. Записи, включенные в запрос экспорта, — это только те, которые были соединены с момента предыдущего выполнения экспорта. 
Запрос на экспорт может содержать [таблицы измерений](../../concepts/fact-and-dimension-tables.md) , в которых *все* записи таблицы измерения включены во *все* запросы экспорта. 
    * При использовании соединений между таблицами фактов и измерений при непрерывном экспорте следует помнить, что записи в таблице фактов обрабатываются только один раз. Если экспорт выполняется, а записи в таблицах измерения отсутствуют для некоторых ключей, записи для соответствующих ключей либо будут пропущены, либо будут содержать значения NULL для столбцов измерений в экспортируемых файлах (в зависимости от того, использует ли запрос внутреннее или внешнее соединение). Свойство Форцедлатенци в определении непрерывного экспорта может быть полезным в таких случаях, где таблицы фактов и измерений принимаются в одно и то же время (для совпадающих записей).
    * Непрерывный экспорт только таблиц измерений не поддерживается. Запрос на экспорт должен включать по крайней мере одну таблицу фактов.
    * Синтаксис явно объявляет, какие таблицы находятся в области (факт), а какие нет в области (измерение). Дополнительные сведения см. в описании параметра `over` в [команде Create](#create-or-alter-continuous-export).

* Количество файлов, экспортируемых в каждой итерации непрерывного экспорта, зависит от того, как была секционирована внешняя таблица. Дополнительные сведения см. в подразделе "Примечания" в [команде "экспорт в внешнюю таблицу](export-data-to-an-external-table.md) ". Обратите внимание, что каждая итерация непрерывного экспорта всегда записывает данные в *новые* файлы и никогда не добавляет к существующим. В результате количество экспортируемых файлов также зависит от частоты выполнения непрерывного экспорта (`intervalBetweenRuns` параметр).

Всем командам непрерывного экспорта требуются [разрешения администратора базы данных](../access-control/role-based-authorization.md).

## <a name="create-or-alter-continuous-export"></a>Создание или изменение непрерывного экспорта

**Синтаксис**

`.create-or-alter``continuous-export` *Континуаусекспортнаме* <br>
[ `over` `(` *T1*, *T2* `)`] <br>
`to``table` *Екстерналтабленаме* <br> [ `with` `(` *PropertyName* PropertyName `=` *PropertyValue*PropertyValue`,`... `)`]<br>
\<| *Выбор*

**Свойства**:

| Свойство             | Тип     | Описание   |
|----------------------|----------|---------------------------------------|
| континуаусекспортнаме | Строка   | Имя непрерывного экспорта. Имя должно быть уникальным в пределах базы данных и используется для периодического выполнения непрерывного экспорта.      |
| екстерналтабленаме    | Строка   | Имя [внешней таблицы](../externaltables.md) для экспорта.  |
| Запрос                | Строка   | Запрос для экспорта.  |
| Over (T1, T2)        | Строка   | Необязательный список таблиц фактов с разделителями-запятыми в запросе. Если не указано, то все таблицы, на которые имеются ссылки в запросе, считаются таблицами фактов. Если указано, таблицы, *не* входящие в этот список, рассматриваются как таблицы измерений и не будут находиться в области (все записи будут участвовать во всех экспортах). Дополнительные сведения см. в [разделе "Примечания](#notes) ". |
| интервалбетвинрунс  | Временной диапазон | Интервал времени между выполнением непрерывного экспорта. Должно быть больше 1 минуты.   |
| форцедлатенци        | Временной диапазон | Необязательный период времени для ограничения запроса записями, которые были приняты только до этого периода (относительно текущего времени). Это полезно, если, например, запрос выполняет некоторые агрегаты или объединения и перед выполнением экспорта вы хотите убедиться, что все соответствующие записи уже были приняты.

Помимо описанных выше, все свойства, поддерживаемые в [команде Export во внешней таблице](export-data-to-an-external-table.md) , поддерживаются командой непрерывного экспорта. 

**Пример.**

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| Имя     | екстерналтабленаме | Запрос | форцедлатенци | интервалбетвинрунс | курсорскопедтаблес         | експортпропертиес                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| мекспорт | екстерналблоб      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']. ["]"<br>] | {<br>  "Сизелимит": 104857600<br>} |

## <a name="show-continuous-export"></a>Показывать непрерывный экспорт

**Синтаксис**

`.show``continuous-export` *Континуаусекспортнаме*

Возвращает свойства непрерывного экспорта *континуаусекспортнаме*. 

**Свойства**

| Свойство             | Тип   | Описание                |
|----------------------|--------|----------------------------|
| континуаусекспортнаме | Строка | Имя непрерывного экспорта. |


`.show` `continuous-exports`

Возвращает все непрерывные операции экспорта в базе данных. 

**Проверки**

| Выходной параметр    | Тип     | Описание                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| курсорскопедтаблес  | Строка   | Список явно заданных таблиц (фактов) (сериализованных JSON)               |
| експортпропертиес    | Строка   | Свойства экспорта (сериализованные JSON)                                     |
| експортедто          | Дата и время | Последнее успешно экспортированное значение DateTime (время приема)       |
| екстерналтабленаме   | Строка   | Имя внешней таблицы                                              |
| форцедлатенци       | TimeSpan | Принудительная задержка (значение null, если не указано)                                   |
| интервалбетвинрунс | TimeSpan | Интервал между запусками                                                   |
| IsDisabled          | Логическое  | True, если непрерывный экспорт отключен                               |
| IsRunning           | Логическое  | True, если непрерывный экспорт выполняется в данный момент                      |
| ластрунресулт       | Строка   | Результаты последнего выполнения непрерывного экспорта (`Completed` или) `Failed` |
| ластрунтиме         | Дата и время | Время последнего выполнения непрерывного экспорта (время начала)           |
| Имя                | Строка   | Имя непрерывного экспорта                                           |
| Запрос               | Строка   | Экспорт запроса                                                            |
| старткурсор         | Строка   | Начальная точка первого выполнения этого непрерывного экспорта         |

## <a name="show-continuous-export-exported-artifacts"></a>Показывать экспортированные артефакты непрерывного экспорта

**Синтаксис**

`.show``continuous-export` *Континуаусекспортнаме*`exported-artifacts`

Возвращает все артефакты, экспортированные непрерывным экспортом во всех запусках. Рекомендуется отфильтровать результаты по столбцу timestamp в команде, чтобы просмотреть только интересующие записи. История экспортированных артефактов сохраняется в течение 14 дней. 

**Свойства**

| Свойство             | Тип   | Описание                |
|----------------------|--------|----------------------------|
| континуаусекспортнаме | Строка | Имя непрерывного экспорта. |

**Проверки**

| Выходной параметр  | Тип     | Описание                            |
|-------------------|----------|----------------------------------------|
| Отметка времени         | Datetime | Метка времени выполнения непрерывного экспорта |
| екстерналтабленаме | Строка   | Имя внешней таблицы             |
| Path              | Строка   | Путь вывода                            |
| нумрекордс        | long     | Число записей, экспортируемых по пути     |

**Пример.** 

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Отметка времени                   | екстерналтабленаме | Path             | нумрекордс | сизеинбитес |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31:30.2634216 | екстерналблоб      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |

## <a name="show-continuous-export-failures"></a>Показывать ошибки непрерывного экспорта

**Синтаксис**

`.show``continuous-export` *Континуаусекспортнаме*`failures`

Возвращает все ошибки, зарегистрированные в процессе непрерывного экспорта. Отфильтруйте результаты по столбцу timestamp в команде, чтобы просмотреть только интересующий вас диапазон времени. 

**Свойства**

| Свойство             | Тип   | Описание                |
|----------------------|--------|----------------------------|
| континуаусекспортнаме | Строка | Имя непрерывного экспорта  |

**Проверки**

| Выходной параметр | Тип      | Описание                                         |
|------------------|-----------|-----------------------------------------------------|
| Отметка времени        | Datetime  | Метка времени сбоя.                           |
| Операции      | Строка    | ИДЕНТИФИКАТОР операции сбоя.                    |
| Имя             | Строка    | Имя непрерывного экспорта.                             |
| ластсукцессрун   | Отметка времени | Последнее успешное выполнение непрерывного экспорта.   |
| фаилурекинд      | Строка    | Сбой/Партиалфаилуре. Партиалфаилуре указывает, что некоторые артефакты были успешно экспортированы до возникновения сбоя. |
| Сведения          | Строка    | Сведения об ошибке сбоя.                              |

**Пример.** 

```kusto
.show continuous-export MyExport failures 
```

| Отметка времени                   | Операции                          | Имя     | ластсукцессрун              | фаилурекинд | Сведения    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07:41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | мекспорт | 2019-01-01 11:06:35.6308140 | Сбой     | Сведения... |

## <a name="drop-continuous-export"></a>Удалить непрерывный экспорт

**Синтаксис**

`.drop``continuous-export` *Континуаусекспортнаме*

**Свойства**

| Свойство             | Тип   | Описание                |
|----------------------|--------|----------------------------|
| континуаусекспортнаме | Строка | Имя непрерывного экспорта |

**Проверки**

Оставшиеся непрерывные операции экспорта в базе данных (после удаления). Выходная схема, как в [команде "показывать непрерывный экспорт](#show-continuous-export)".

## <a name="disable-or-enable-continuous-export"></a>Включение и отключение непрерывного экспорта

**Синтаксис**

`.enable``continuous-export` *Континуаусекспортнаме* 

`.disable``continuous-export` *Континуаусекспортнаме* 

Можно отключить или включить задание непрерывного экспорта. Отключенный непрерывный экспорт не выполняется, но его текущее состояние сохраняется и может возобновляться при включении непрерывного экспорта. При включении непрерывного экспорта, который был отключен в течение длительного времени, при отключении экспорт продолжится с места его последнего останова. Это может привести к длительному выполнению экспорта, блокирующему выполнение других операций экспорта, если для обслуживания всех процессов недостаточно емкости кластера. Непрерывные экспорты выполняются с помощью времени последнего выполнения в возрастающем порядке (сначала выполняется самый старый экспорт, пока не будет завершено выполнение операции). 

**Свойства**

| Свойство             | Тип   | Описание                |
|----------------------|--------|----------------------------|
| континуаусекспортнаме | Строка | Имя непрерывного экспорта |

**Проверки**

Результат [команды "показывать непрерывный экспорт](#show-continuous-export) " для измененного непрерывного экспорта. 




## <a name="exporting-historical-data"></a>Экспорт исторических данных

Непрерывный экспорт начинает экспорт данных только с момента создания. Записи, полученные до этого времени, следует экспортировать отдельно с помощью команды (не непрерывной) [экспорта](export-data-to-an-external-table.md). Чтобы избежать дублирования данных, экспортированных с помощью непрерывного экспорта, используйте Старткурсор, возвращенную [командой "показывать непрерывный экспорт](#show-continuous-export) ", и экспортируйте только записи, в которых cursor_before_or_at значение курсора. См. пример ниже. Исторические данные могут быть слишком большими для экспорта в одной команде экспорта. Поэтому Разбейте запрос на несколько небольших пакетов. 

```kusto
.show continuous-export MyExport | project StartCursor
```

| старткурсор        |
|--------------------|
| 636751928823156645 |

За которым следует: 

```kusto
.export async to table ExternalBlob
<| T | where cursor_before_or_at("636751928823156645")
```