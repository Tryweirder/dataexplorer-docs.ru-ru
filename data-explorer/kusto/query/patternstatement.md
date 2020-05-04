---
title: Инструкция Pattern — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается инструкция Pattern в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 97fc8361feb3d8dddb7d0722ce741ef44bd4cec6
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737272"
---
# <a name="pattern-statement"></a>pattern, инструкция

::: zone pivot="azuredataexplorer"

**Шаблон** представляет собой именованную конструкцию, похожую на представление, которая сопоставляет предопределенные строковые кортежи с телом функций без параметров. Шаблоны уникальны в двух аспектах:

* Шаблоны вызываются с помощью синтаксиса, который напоминает ссылки на таблицу в области.
* Шаблоны имеют управляемый, близкий к себе набор значений аргументов, которые могут быть сопоставлены, а процесс сопоставления выполняется Kusto. Это означает, что шаблон объявлен, но не определен, Kusto выявление и пометка в качестве ошибки для всех вызовов шаблона, что позволяет «разрешать» эти закономерности в приложении среднего уровня.


## <a name="pattern-declaration"></a>Объявление шаблона
Оператор Pattern используется для объявления или определения шаблона.
Например, ниже приведен оператор pattern, объявляющий `app` как шаблон:

```kusto
declare pattern app;
```

Эта инструкция указывает Kusto, `app` что является шаблоном, но не указывает Kusto, как разрешить шаблон. В результате любая попытка вызвать этот шаблон в запросе приведет к возникновению определенной ошибки с перечнем всех таких вызовов. Например:

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Этот запрос выдаст ошибку от Kusto, указывающую, что не удается разрешить следующие вызовы шаблона: `app("ApplicationX")["StartEvents"]` и. `app("ApplicationX")["StopEvents"]`

**Синтаксис**

`declare``pattern` *Паттерннаме*

## <a name="pattern-definition"></a>Определение шаблона

Для определения шаблона можно также использовать инструкцию pattern. В определении шаблона все возможные вызовы шаблона явным образом размещаются, а соответствующее табличное выражение задается. Когда Kusto выполняет запрос, он заменяет каждый вызов шаблона соответствующим телом шаблона. Например:

```kusto
declare pattern app = (applicationId:string)[eventType:string]
{
    ("ApplicationX").["StopEvents"] = { database("AppX").Events | where EventType == "StopEvent" };
    ("ApplicationX").["StartEvents"] = { database(applicationId).Events | where EventType == eventType } ;
};
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Выражение, предоставляемое для каждого сопоставляемого шаблона, является либо именем таблицы, либо ссылкой на [инструкцию Let](letstatement.md).

**Синтаксис**

`declare``pattern` *PatternName*Паттерннаме = *ArgName* аргнаме `:` *аргтипе* [`,` ..`(`.] `)` [`[` *PathName* PathName `:` *PathArgType* пасаргтипе `]`]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [`,` *ArgValue2* ...] `)` [ `.[` * Пасвалуе `]` ] `=` `{` *выражение* `(` *ArgValue1_2* *ArgValue2_2* [ArgValue1_2 [`,` ArgValue2_2.. &nbsp; .]`};` &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; `)` [ `.[` *PathValue_2* `{` *expression_2* ] `=` expression_2`};` .. &nbsp; . `]` &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *Паттерннаме*: имя ключевого слова pattern. Синтаксис, определяющий ключевое слово only, допустим: для обнаружения всех ссылок на шаблоны с указанным ключевым словом.
* *Аргнаме*: имя аргумента шаблона. Шаблоны допускают одно или несколько имен аргументов.
* *Аргтипе*: тип аргумента шаблона (в настоящее время `string` допускается только)
* *PathName*: имя аргумента пути. Шаблоны допускают ноль или одно имя пути.
* *Пастипе*: тип аргумента пути (в настоящее время `string` допускается только)
* *ArgValue1*, *ArgValue2*,...-значения аргументов шаблона (в настоящее время разрешены только `string` литералы)
* *Пасвалуе* — значение пути к шаблону (в настоящее `string` время разрешены только литералы)
* *выражение*: *выражение* — табличное выражение (например, `Logs | where Timestamp > ago(1h)`) или лямбда-выражение, которое ссылается на функцию.

## <a name="pattern-invocation"></a>Вызов шаблона

Синтаксис вызова шаблона аналогичен синтаксису ссылок на таблицы в заданной области:

* *Паттерннаме* `(` *ArgValue1* [`,` *ArgValue2* ...] `).` *Пасвалуе*
* *Паттерннаме* `(` *ArgValue1* [`,` *ArgValue2* ...] `).["` *Пасвалуе*`"]`

## <a name="remarks"></a>Remarks

**Сценарий**

Оператор Pattern предназначен для приложений среднего уровня, принимающих запросы пользователей, а затем отправляет эти запросы в Kusto. Такие приложения часто добавляют к этим пользовательским запросам логическую модель схемы (набор [инструкций Let](letstatement.md), возможно, с суффиксом [оператора restrict](restrictstatement.md)).
В некоторых случаях этим приложениям нужен синтаксис, позволяющий пользователям ссылаться на сущности, которые не могут быть известны заранее, и определяться в логической схеме, которую они создают (так как они не известны заранее), из-за того, что количество потенциальных сущностей слишком велико, чтобы быть предварительно определенным в логической схеме.

Шаблон разрешает этот сценарий следующим образом. Приложение среднего уровня отправляет запрос в Kusto со всеми объявленными, но не определенными шаблонами. Затем Kusto анализирует запрос и, если в нем есть один или несколько вызовов шаблона, возвращает ошибку обратно в приложение среднего уровня со всеми такими вызовами, которые явно перечислены в списке. Затем приложение среднего уровня может разрешить каждую из этих ссылок и повторно выполнить запрос, представив в нем предварительно уточненное определение шаблона.

**Нормализации**

Kusto автоматически нормализует шаблон, так что, например, ниже приведены все вызовы одного и того же шаблона, и один из них сообщается обратно:

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Это также означает, что один из них не может определить их вместе, так как они считаются одинаковыми.

**Подстановочные знаки**

Kusto не обрабатывает подстановочные знаки в шаблоне каким-либо особым способом. Например, в следующем запросе:

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto будет сообщать об одном отсутствующем вызове `app("ApplicationX").["*"]`шаблона:.

## <a name="examples"></a>Примеры

Запросы к более чем одному вызову шаблона:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|Приложение|сометекст|
|---|---|
|#1 приложений|Это бесплатный текст: 1|
|#1 приложений|Это бесплатный текст: 2|
|#1 приложений|Это бесплатный текст: 3|
|#1 приложений|Это бесплатный текст: 4|
|#1 приложений|Это бесплатный текст: 5|
|#2 приложений|Это бесплатный текст: 9|
|#2 приложений|Это бесплатный текст: 8|
|#2 приложений|Это бесплатный текст: 7|
|#2 приложений|Это бесплатный текст: 6|
|#2 приложений|Это бесплатный текст: 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

Семантическая ошибка:

     SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a1').['Text']","App('a2').['Text']"].

```kusto
declare pattern App;
declare pattern App = (applicationId:string)[scope:string]  
{
    ('a1').['Data']    = { range x from 1 to 5 step 1 | project App = "App #1", Data    = x };
    ('a1').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #1", Metrics = rand() };
    ('a2').['Data']    = { range x from 1 to 5 step 1 | project App = "App #2", Data    = 10 - x };
    ('a3').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #3", Metrics = rand() };
};
union (App('a2').Metrics), (App('a3').Metrics) 
```

Возвращена семантическая ошибка:

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
