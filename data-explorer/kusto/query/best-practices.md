---
title: Рекомендации по запросам - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны рекомендации по запросам в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 3458c2fcd7fab3345f65393d7e9a13e844088a9f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663856"
---
# <a name="query-best-practices"></a>Рекомендации по запросам 

## <a name="general"></a>Общее

Есть несколько "Dos and Don'ts" вы можете следовать, чтобы заставить вас запрос работать быстрее.

### <a name="do"></a>Как нужно

*   Сначала используйте фильтры времени. Kusto высоко оптимизирован для использования временных фильтров.
*   При использовании струнных операторов:
    *   Предпочитаю `has` оператора, `contains` когда ищите полные токены. `has`является более performant, поскольку он не должен искать для подстроки.
    *   Предпочитаюиспользовать использование чувствительных к случаям операторов, когда это применимо, так как они более эффективны. `==` Например, предпочитают использовать `in~`более, `contains` `contains` / `contains_cs` `has` / `=~` `in` снова и `contains_cs` снова (но если вы можете избежать вообще и использовать, `has_cs`это еще лучше).
*   Предпочитаю смотреть в определенном `*` столбце, а не использовать (полный поиск текста во всех столбцах)
*   Если вы обнаружите, что большинство запросов касаются извлечения полей из [динамических объектов](./scalar-data-types/dynamic.md) через миллионы строк, подумайте о том, чтобы материализовать эту колонку во время приема. Таким образом, вы будете платить только один раз за извлечение колонки.  
*   Если у `let` вас есть утверждение, значение которого вы используете более одного раза, рассмотрите `materialize()`возможность использования [функции Materialize(см.](./materializefunction.md) некоторые [рекомендации](#materialize-function) по использованию).

### <a name="dont"></a>Как не нужно

*   Не пробуйте новые `limit [small number]` `count` запросы без или в конце.
    Запуск несвязанных запросов по неизвестному набору данных может привести к возврату ГБ результатов клиенту, что приведет к медленному реагированию и занятию кластера.
*   Если вы обнаружите, что применяете конверсии (JSON, string и т.д.) более 1 миллиарда записей - измените свой запрос, чтобы уменьшить объем данных, подаваемых в преобразование.
*   Не используйте `tolower(Col) == "lowercasestring"` для сравнения случаев бесчувственные. Вместо этого используйте `Col =~ "lowercasestring"`.
    *   Если ваши данные уже в нижнем регистре (или верхнем регистре), то избегайте использования нечувствительных сравнений случаев и используйте `Col == "lowercasestring"` (или) `Col == "UPPERCASESTRING"`вместо этого.
*   Не фильтруйте на вычисленной колонке, если можно отфильтровать на столбце таблицы. Другими словами: `T | extend _value = <expression> | where predicate(_value)`вместо, `T | where predicate(<expression>)`делать: .

## <a name="summarize-operator"></a>Оператор summarize

*   Когда группа по клавишам суммировать оператора с высокой кардинальностью (лучшая практика: выше 1 млн.), рекомендуется использовать [hint.strategy.shuffle](./shufflequery.md).

## <a name="join-operator"></a>Оператор join

*   При использовании [оператора соединения](./joinoperator.md)выберите таблицу с меньшим количеством строк, чтобы быть первой (слева-большинство). 
*   При использовании данных [оператора соединения](./joinoperator.md) в кластерах запустите запрос на «правой» стороне соединения (где находится большая часть данных).
*   Когда левая сторона мала (до 100 000 записей), а правая сторона большая, используйте [hint.strategy.broadcast](./broadcastjoin.md).
*   Когда обе стороны соединения слишком велики, а ключ соединения с высокой кардинальностью, используйте [hint.strategy'shuffle](./shufflequery.md).
    
## <a name="parse-operator-and-extract-function"></a>разбор оператора и экстракта() функции

*   [оператор разбора](./parseoperator.md) (простой режим) полезен, когда значения в целевом столбце содержат строки, которые имеют один и тот же формат или шаблон.
Например, для столбца с `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`такими значениями, как , при `parse` извлечении `extract()` значений каждого поля, используйте оператора вместо нескольких инструкций.
*   [функция экстракта ()](./extractfunction.md) полезна, когда разбираенные строки не все следуют тому же формату или шаблону.
В таких случаях извлекайте требуемые значения с помощью REGEX.

## <a name="materialize-function"></a>материализовать () функцию

*   При использовании [функции материализации ()](./materializefunction.md)постарайтесь подтолкнуть всех возможных операторов, которые уменьшат материализованный набор данных и при этом сохраняют семантику запроса. Например, фильтры или проект нужны только столбцы.
    
    **Примере:**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* Фильтр на тексте является взаимным и может быть толкнул материализовать выражение.
    Запросу нужны только эти `Timestamp` `Text`столбцы, `Resource1` и `Resource2` поэтому рекомендуется проецировать эти столбцы внутри материализованного выражения.
    
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

*   Возможно, стоит (когда комбинированный фильтр резко снижает материализованный результат) объединить оба фильтра `or` на материализованном результате логическим выражением, как в запросе ниже. Тем не менее, сохранить фильтры в каждой ноге соединения, чтобы сохранить семантику запроса:
     
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
    