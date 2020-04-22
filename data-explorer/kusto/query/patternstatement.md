---
title: заявление о шаблонах - Исследователь данных Azure Документы Майкрософт
description: В этой статье описано заявление о шаблонах в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d7ad021fe7b458d54beeb24b908420324b45ad39
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765666"
---
# <a name="pattern-statement"></a>заявление шаблона

::: zone pivot="azuredataexplorer"

**Шаблон** — это именованная конструкция, похожая на представление, которая отображает предопределенные строки tuples для безпаративных тел функций. Шаблоны уникальны в двух аспектах:

* Шаблоны "вызываются" с помощью синтаксиса, напоминающий ссылки на таблицы.
* Шаблоны имеют контролируемый, закрытый набор значений аргументов, которые могут быть отображены, и процесс отображения выполняется Kusto. Это означает, что если шаблон объявлен, но не определен, Kusto идентифицирует и помечает как ошибку все вызовы шаблона, что позволяет «разрешить» эти шаблоны с помощью приложения среднего уровня.


## <a name="pattern-declaration"></a>Декларация шаблонов
Заявление шаблона используется для объявления или определения шаблона.
Например, ниже приводится шаблон, `app` который объявляется шаблоном:

```kusto
declare pattern app;
```

Это утверждение говорит Кусто, что `app` это шаблон, но не говорит Kusto, как решить шаблон. В результате любая попытка вызвать этот шаблон в запросе приведет к конкретной ошибке, перечисляя все такие вызовы. Пример:

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

Этот запрос будет генерировать ошибку от Kusto, указывая, что `app("ApplicationX")["StartEvents"]` следующие вызовы шаблона не могут быть решены: и `app("ApplicationX")["StopEvents"]`.

**Синтаксис**

`declare``pattern` *Имя шаблона*

## <a name="pattern-definition"></a>Определение шаблона

Заявление шаблона также может быть использовано для определения шаблона. В определении шаблона явно изложены все возможные вызовы шаблона и приведено соответствующее таблетческое выражение. Когда Kusto выполняет запрос, он заменяет каждый вызов шаблона соответствующим органом шаблона. Пример:

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

Выражение, которое предусмотрено для каждого сопоставления шаблона, является либо именем таблицы, либо ссылкой на [оператора Let.](letstatement.md)

**Синтаксис**

`declare``pattern` *PatternName* = *ArgName* `:` *ArgType* `,` ArgName ArgType`(` `)` -`[` *PathName* `:` *PathArgType* `]``{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* `,` - *ArgValue2* ... `)` Выражение `.[` «Патвалу» `]` `=` `{` *expression* `};` &nbsp; &nbsp; `,` *ArgValue1_2* ArgValue1_2 &nbsp; *ArgValue2_2* ArgValue2_2 &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp; `(` `)` `.[` *- PathValue_2* `]` `=` `{` *expression_2* expression_2`};` &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *PatternName*: Название ключевого слова шаблона. Синтаксис, определяющий только ключевое слово, разрешен: для обнаружения всех ссылок шаблонов с указанным ключевым словом.
* *ArgName*: Название аргумента шаблона. Шаблоны позволяют одно или несколько имен аргументов.
* *ArgType*: Тип аргумента шаблона (в настоящее время разрешен только) `string`
* *PathName*: Название аргумента пути. Шаблоны позволяют ноль или одно имя пути.
* *PathType*: Тип аргумента пути `string` (в настоящее время разрешен только)
* *ArgValue1*, *ArgValue2*, ... - значения шаблона `string` аргументы (в настоящее время допускаются только буквальные)
* *PathValue* - значение пути шаблона `string` (в настоящее время разрешены только буквальные буквы)
* *выражение*: *Выражение* - таблолярное `Logs | where Timestamp > ago(1h)`выражение (например, ), или выражение lambda которое ссылается функция.

## <a name="pattern-invocation"></a>Вызов шаблона

Синтаксис вызова шаблона аналогичен синтаксису с учетом таблицы:

* *PatternName* `(` *ArgValue1* -`,` *ArgValue2* ..." `).` *PathValue*
* *PatternName* `(` *ArgValue1* -`,` *ArgValue2* ..." `).["` *PathValue*`"]`

## <a name="remarks"></a>Remarks

**Сценарий**

Заявление шаблона предназначено для приложений среднего уровня, которые принимают запросы пользователей, а затем отправляют эти запросы в Kusto. Такие приложения часто префиксируют эти запросы пользователей с логической моделью схемы (набор [инструкций,](letstatement.md)возможно, суффиксированных [оператором ограничения).](restrictstatement.md)
В некоторых случаях эти приложения нуждаются в синтаксисе, который они могут предоставить пользователям для ссылок, которые не могут быть известны заранее и определены в логической схеме, которую они строят (либо потому, что они не известны заранее, из-за того, что число потенциальных сущностей слишком велико, чтобы быть предварительно определенным в логической схеме.

Шаблон решить этот сценарий следующим образом. Приложение среднего уровня отправляет запрос в Kusto со всеми заявленными шаблонами, но не определенными. Затем Kusto разбирает запрос, и если в нем есть один или несколько вызовов шаблонов, он возвращает ошибку обратно в приложение среднего уровня со всеми такими призывами, явно перечисленными. Приложение среднего уровня может разрешить каждую из этих ссылок и повторно запустить запрос, на этот раз префиксировав его с полностью разработанным определением шаблона.

**Нормализация**

Kusto автоматически нормализует шаблон, так что, например, все вызовы одного и того же шаблона, и один из них сообщается обратно:

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

Это также означает, что нельзя определить их вместе, так как они считаются одинаковыми.

**Подстановочные знаки**

Кусто не относится к подстановочным знакам по-своему. Например, в следующем запросе:

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto сообщит о одном недостающих вызовах шаблона: `app("ApplicationX").["*"]`.

## <a name="examples"></a>Примеры

Запросы по более чем одному шаблону вызова:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|Приложение|SomeText|
|---|---|
|Приложение #1|Это бесплатный текст: 1|
|Приложение #1|Это бесплатный текст: 2|
|Приложение #1|Это бесплатный текст: 3|
|Приложение #1|Это бесплатный текст: 4|
|Приложение #1|Это бесплатный текст: 5|
|Приложение #2|Это бесплатный текст: 9|
|Приложение #2|Это бесплатный текст: 8|
|Приложение #2|Это бесплатный текст: 7|
|Приложение #2|Это бесплатный текст: 6|
|Приложение #2|Это бесплатный текст: 5|

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

Семантическая ошибка возвращена:

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
