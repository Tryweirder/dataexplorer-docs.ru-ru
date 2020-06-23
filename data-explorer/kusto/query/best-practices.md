---
title: Рекомендации по запросам в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются рекомендации по запросам в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: e6db181188250d28689cca64762e13973f2f33f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128824"
---
# <a name="best-practices"></a>Рекомендации 

## <a name="general"></a>Общие сведения

Существует несколько «DOS и неограниченных» действий, которые можно использовать для ускорения выполнения запросов.

### <a name="do"></a>Как нужно

*   Сначала используйте фильтры времени. Kusto высоко оптимизирована для использования фильтров времени.
*   При использовании строковых операторов:
    *   `has` `contains` При поиске полных маркеров предпочитать оператор. `has`более производительно, так как не нужно искать подстроки.
    *   Предпочитать использовать операторы с учетом регистра, когда это применимо, так как они являются более производительными. Например, вы предпочитаете использовать `==` более `=~` , не `in` более `in~` , а также `contains_cs` `contains` (но если вы можете `contains` / `contains_cs` вообще избежать и использовать `has` / `has_cs` , это еще лучше).
*   Предпочтительно искать в определенном столбце, а не использовать `*` (полнотекстовый поиск по всем столбцам).
*   Если вы обнаружите, что большая часть ваших запросов имеет дело с извлечением полей из [динамических объектов](./scalar-data-types/dynamic.md) в миллионах строк, рассмотрите возможность материализации этого столбца во время приема. Таким образом вы будете платить только один раз для извлечения столбцов.  
*   Если имеется `let` оператор, значение которого используется несколько раз, рассмотрите возможность использования [функции материализации ()](./materializefunction.md) (см. Некоторые [рекомендации](#materialize-function) по использованию `materialize()` ).

### <a name="dont"></a>Не рекомендуется

*   Не пытайтесь использовать новые запросы без `limit [small number]` или `count` в конце.
    Выполнение непривязанных запросов по неизвестному набору данных может привести к возврату ГБ результатов клиенту, что приведет к снижению отклика и доступности кластера.
*   Если вы обнаружите, что вы применяете преобразования (JSON, String и т. д.) свыше 1 000 000 000 записей, измените запрос, чтобы уменьшить объем данных, поступающих в преобразование.
*   Не используйте `tolower(Col) == "lowercasestring"` для сравнения без учета регистра. Вместо этого используйте `Col =~ "lowercasestring"`.
    *   Если данные уже находятся в нижнем регистре (или в верхнем регистре), старайтесь не использовать сравнения без учета регистра и `Col == "lowercasestring"` вместо этого использовать (или `Col == "UPPERCASESTRING"` ).
*   Не фильтруйте по вычисляемому столбцу, если можно выполнить фильтрацию по столбцу таблицы. Другими словами: вместо `T | extend _value = <expression> | where predicate(_value)` , do: `T | where predicate(<expression>)` .

## <a name="summarize-operator"></a>Оператор summarize

*   Если ключи group by оператора суммирования имеют большую кратность (рекомендуется использовать выше 1 000 000), рекомендуется воспользоваться [указанием. Стратегия = в случайном порядке](./shufflequery.md).

## <a name="join-operator"></a>Оператор join

*   При использовании [оператора Join](./joinoperator.md)выберите таблицу с меньшим числом строк, чтобы она была первой (самая левая). 
*   При использовании [объединения данных оператора](./joinoperator.md) в кластерах выполните запрос на стороне "Right" объединения (где находятся большинство данных).
*   Если левая часть невелика (до 100 000 записей), а правая часть велика, используйте [подсказку. Стратегия = Broadcast](./broadcastjoin.md).
*   Если обе стороны объединения слишком велики, а ключ объединения имеет большую кратность, используйте [Указание. Стратегия = случайный](./shufflequery.md).
    
## <a name="parse-operator-and-extract-function"></a>Оператор Parse и Функция Extract ()

*   [оператор Parse](./parseoperator.md) (простой режим) полезен, если значения в целевом столбце содержат строки, имеющие одинаковый формат или шаблон.
Например, для столбца со значениями `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` , например, при извлечении значений каждого поля, используйте `parse` оператор вместо нескольких `extract()` операторов.
*   [Функция Extract ()](./extractfunction.md) полезна, если проанализированные строки не имеют того же формата или шаблона.
В таких случаях извлеките необходимые значения с помощью регулярного выражения.

## <a name="materialize-function"></a>Функция материализации ()

*   При использовании [функции материализации ()](./materializefunction.md)попробуйте отправить все возможные операторы, которые уменьшают набор материализованных данных и по-прежнему будут сохранять семантику запроса. Например, фильтры или проектные обязательные столбцы.
    
    **Пример**.

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* Фильтр по тексту является взаимным и может быть передан в материализовать выражение.
    Запросу требуются только эти столбцы `Timestamp` , `Text` `Resource1` и `Resource2` поэтому рекомендуется проецировать эти столбцы внутри материализованных выражений.
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   Если фильтры не идентичны, как в этом запросе:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   Это может быть целесообразно (если комбинированный фильтр значительно сокращает объем материализованных результатов), чтобы объединить оба фильтра в материализованный результат по логическому `or` выражению, как показано в запросе ниже. Тем не менее, сохраните фильтры в каждой части объединения, чтобы сохранить семантику запроса:
     
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```
    