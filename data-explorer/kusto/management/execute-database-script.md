---
title: . выполнение скрипта базы данных в Azure обозреватель данных
description: В этой статье описываются функциональные возможности скрипта выполнения базы данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: 667fcc87a1e301bdcceb227bb99ad70d62f46153
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320831"
---
# <a name="execute-database-script"></a>.execute database script

Выполняет пакет команд управления в области одной базы данных.

## <a name="syntax"></a>Синтаксис

`.execute` `database` `script`  
[ `with` `(` *PropertyName* `=` *propertyvalue* [ `,` ...] `)` ]   
`<|`  
 *Control-Commands-Script*

### <a name="parameters"></a>Параметры

* *Control-Commands-Script*: текст с одной или несколькими командами управления.
* *Область базы данных*: скрипт применяется к *области базы данных* , указанной как часть контекста запроса.

### <a name="optional-properties"></a>Необязательные свойства

| Свойство            | Тип            | Описание                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `ContinueOnErrors`            | `bool`        | Если задано значение, `false` скрипт будет останавливаться при первой ошибке. Если задано значение `true` — выполнение скрипта продолжится. Значение по умолчанию: `false`. |

## <a name="output"></a>Выходные данные

Каждая команда, показываемая в скрипте, будет отображаться как отдельная запись в выходной таблице. Каждая запись имеет следующие поля:

|Выходной параметр |Тип |Описание
|---|---|--- 
|Операции  |Guid |Идентификатор команды.
|CommandType  |Строка |Тип команды.
|CommandText  |Строка |Текст конкретной команды.
|Результат|Строка|Результат выполнения определенной команды.
|Причина|Строка|Подробные сведения о результатах выполнения команды.

>[!NOTE]
>* Текст скрипта может содержать пустые строки и комментарии между командами.
>* Команды выполняются последовательно, в порядке их следования во входном скрипте.
>* Выполнение скрипта не является транзакционным, и при возникновении ошибки откат не выполняется. Рекомендуется использовать идемпотентными форму команд при использовании `.execute database script` .
>* Поведение команды по умолчанию — Ошибка при первой ошибке, его можно изменить с помощью аргумента Property.
>* Команды управления только для чтения ( `.show` команды) не выполняются и включаются в отчет о состоянии `Skipped` .

## <a name="example"></a>Пример

```kusto
.execute database script <|
//
// Create tables
.create-merge table T(a:string, b:string)
//
// Apply policies
.alter-merge table T policy retention softdelete = 10d 
//
// Create functions
.create-or-alter function
  with (skipvalidation = "true") 
  SampleT1(myLimit: long) { 
    T1 | limit myLimit
}
```

|Операции|CommandType|CommandText|Результат|Причина|
|---|---|---|---|---|
|1d28531b-58c8-4023-a5d3-16fa73c06cfa|таблекреате|. CREATE — merge таблица T (а:стринг, б:стринг)|Завершено||
|67d0ea69-baa4-419a-93d3-234c03834360|ретентионполициалтер|. ALTER-Merge таблица T политика хранения софтделете = 10D|Завершено||
|0b0e8769-d4e8-4ff9-adae-071e52a650c7|функтионкреатеоралтер|.create-or-alter function<br>with (скипвалидатион = "true")<br>SampleT1 (Милимит: long) {<br>\|Ограничение T1 милимит<br>}|Завершено||
