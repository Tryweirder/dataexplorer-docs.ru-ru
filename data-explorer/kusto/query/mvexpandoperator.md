---
title: Оператор mv-expand в Azure Data Explorer
description: В этой статье описывается оператор mv-expand в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.localizationpriority: high
ms.openlocfilehash: e439fff119e005e44a0649fe22cadf3614ce036d
ms.sourcegitcommit: 555f3da35fe250fabd35fcc6014bf055ef8405db
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972504"
---
# <a name="mv-expand-operator"></a>Оператор mv-expand

Развертывание массива с несколькими значениями или контейнера свойств.

Оператор `mv-expand`применяется к столбцу [динамического](./scalar-data-types/dynamic.md) типа с массивом или контейнером свойств, так что каждое значение в коллекции получает отдельную строку. Все остальные столбцы в развернутой строке дублируются. 

## <a name="syntax"></a>Синтаксис

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>Аргументы

* *ColumnName* — в выходных данных массивы из этого столбца будут развернуты в несколько строк. 
* *ArrayExpression* — выражение, которое возвращает массив. Если используется этот аргумент, добавляется новый столбец, а существующий сохраняется.
* *Name* — имя нового столбца.
* *Typename* — указывает базовый тип элементов массива, который станет типом столбца, созданного оператором `mv-expand`. Операция применения типа предназначена только для приведения и не включает синтаксический анализ или преобразование типа. Элементы массива, которые не соответствуют объявленному типу, станут значениями `null`.
* *RowLimit* — максимальное количество строк, созданных из каждой исходной строки. Значение по умолчанию — 2147483647. 

  > [!NOTE]
  > `mvexpand` является прежней версией и устаревшей формой оператора `mv-expand`. В прежней версии по умолчанию используется ограничение в 128 строк.

* *IndexColumnName* — если указан `with_itemindex`, то выходные данные будут включать в себя дополнительный столбец (с именем *IndexColumnName*), содержащий индекс (с началом в 0) элемента в исходной расширенной коллекции. 

## <a name="returns"></a>Возвращаемое значение

Несколько строк для каждого значения в любом массиве, который находится в именованном столбце или выражении массива.
Если указано несколько столбцов или выражений, они развертываются параллельно. Для каждой входной строки указывается столько выходных строк, сколько содержится элементов в наиболее развернутом выражении (более короткие списки заполняются значениями NULL). Если значение в строке является пустым массивом, строка никак не развертывается (не будет отображаться в результирующем наборе). Однако если значение в строке не является массивом, строка сохраняется в результирующем наборе без изменений. 

Тип развернутого столбца всегда динамический. Чтобы выполнить обычное или статистическое вычисление значений, используйте приведение, например `todatetime()` или `tolong()`.

Поддерживаются два режима развертывания контейнера свойств.
* `bagexpansion=bag` или `kind=bag`: контейнеры свойств развертываются в контейнеры свойств с одной записью. Это режим по умолчанию.
* `bagexpansion=array` или `kind=array`: контейнеры свойств развертываются в структуры массива с двумя элементами `[`*key*`,`*value*`]`. Это позволяет единообразно обращаться к ключам и значениям (а также выполнять, например, подсчет разных значений в именах свойств). 

## <a name="examples"></a>Примеры

### <a name="single-column"></a>Один столбец

Простое развертывание одного столбца:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|а|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|

### <a name="zipped-two-columns"></a>Сжатые два столбца

При развертывании двух столбцов применимые столбцы сначала будут сжаты, а затем развернуты:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|а|b|с|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>Декартово произведение двух столбцов

Если вы хотите получить декартово произведение для развертывания двух столбцов, разверните по очереди:

<!-- csl: https://kuskusdfv3.kusto.windows.net/Kuskus -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)
  [
  1,
  dynamic({"prop1":"a", "prop2":"b"}),
  dynamic([5, 6])
  ]
| mv-expand b
| mv-expand c
```

|а|b|с|
|---|---|---|
|1|{  "prop1": "a"}|5|
|1|{  "prop1": "a"}|6|
|1|{  "prop2": "b"}|5|
|1|{  "prop2": "b"}|6|

### <a name="convert-output"></a>Преобразование вывода

Если требуется принудительно развернуть выходные данные mv-expand до определенного типа (по умолчанию — динамический), используйте `to typeof`:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:string, b:dynamic, c:dynamic)["Constant", dynamic([1,2,3,4]), dynamic([6,7,8,9])]
| mv-expand b, c to typeof(int)
| getschema 
```

ColumnName|ColumnOrdinal|DateType|ColumnType
-|-|-|-
а|0|System.String|строка
b|1|System.Object|Динамический
с|2|System.Int32|INT

Обратите внимание, столбец `b` является `dynamic`, а `c` — `int`.

### <a name="using-with_itemindex"></a>Использование with_itemindex

Расширение массива с помощью `with_itemindex`:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1
| summarize x = make_list(x)
| mv-expand with_itemindex=Index x
```

|x|Индекс|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|
 
## <a name="see-also"></a>См. также раздел

* Дополнительные примеры см. в разделе [Диаграмма числа активных действий за период времени](./samples.md#chart-concurrent-sessions-over-time).
* Оператор [mv-apply](./mv-applyoperator.md).
* Оператор [summarize make_list()](makelist-aggfunction.md), который является функцией, противоположной к mv-expand.
* Подключаемый модуль [bag_unpack()](bag-unpackplugin.md) для развертывания динамических объектов JSON в столбцы с помощью ключей контейнера свойств.
