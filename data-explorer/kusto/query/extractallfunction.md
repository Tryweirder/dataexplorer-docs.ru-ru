---
title: extract_all () — обозреватель данных Azure
description: В этой статье описывается extract_all () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 85c118e8cd68c52278a34080eda4936151600cd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247522"
---
# <a name="extract_all"></a>extract_all()

Возвращает все совпадения для [регулярного выражения](./re2.md) из текстовой строки.
При необходимости получите подмножество соответствующих групп.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

## <a name="syntax"></a>Синтаксис

`extract_all(`*регулярное выражение* `,` [*каптуреграупс* `,` ] *текстовая надпись*`)`

## <a name="arguments"></a>Аргументы

|Аргумент        |Описание                                  |Обязательный или необязательный  |
|----------------|---------------------------------------------|----------------------|
|regex           | [Регулярное выражение](./re2.md). Выражение должно иметь по крайней мере одну группу записи и меньше или равно 16 захваченных групп                                                         |Обязательно              |
|каптуреграупс   |Динамическая константа массива, указывающая группу захвата для извлечения. Допустимые значения: от 1 до числа групп записи в регулярном выражении. Также разрешены именованные группы захвата (см. [примеры](#examples)).|Необязательно         |
|text            |A `string` для поиска                         |Обязательно              |

## <a name="returns"></a>Результаты

* Если *Regex* находит совпадение в *тексте*: Возвращает динамический массив, включая все совпадения с указанными группами записи *каптуреграупс*или все группы записи в *регулярном выражении*.
* Если число *каптуреграупс* равно 1: возвращаемый массив имеет одно измерение сопоставленных значений.
* Если число *каптуреграупс* больше 1: возвращаемый массив является двухмерной коллекцией совпадений с несколькими значениями для каждого *каптуреграупс* выбора или всех групп отслеживания, имеющихся в *регулярном выражении* , если *каптуреграупс* опущен.
* Если совпадений нет: `null` .

## <a name="examples"></a>Примеры

### <a name="extract-a-single-capture-group"></a>Извлечение одной группы захвата

Возвращает шестнадцатеричное представление GUID (две цифры в шестнадцатеричной кодировке).

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82", "B8", "4B", "2D", "DF", "A7", "", "D1", "8F", "63", "24", "ad", "26", "D3", "14", "49"]|

### <a name="extract-several-capture-groups"></a>Извлечение нескольких групп захвата 

Использует регулярное выражение с тремя группами записи для разделения каждой части GUID на первую букву, последнюю букву и то, что находится в середине.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id)
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "Fa", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]]|

### <a name="extract-a-subset-of-capture-groups"></a>Извлечение подмножества групп записи

Показывает, как выбрать подмножество групп записи. Регулярное выражение соответствует первой и последней буквам, а также всем остальным. Параметр *каптуреграупс* используется для выбора только первой и последней частей.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "d"], ["d", "7"], ["4", "1"], ["8", "3"], ["2", "9"]]|

### <a name="using-named-capture-groups"></a>Использование именованных групп захвата

В extract_all () можно использовать именованные группы захвата RE2.
*Каптуреграупс* использует индексы группы захвата и ссылку на именованную группу захвата для получения совпадающих значений.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|ID|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "Fa", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]]|
