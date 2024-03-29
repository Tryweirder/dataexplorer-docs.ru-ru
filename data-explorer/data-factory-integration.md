---
title: Интеграция Azure обозреватель данных с фабрикой данных Azure
description: В этом разделе описано, как интегрировать Azure обозреватель данных с фабрикой данных Azure для использования операций копирования, поиска и команд.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/20/2020
ms.openlocfilehash: 18fd9aa351bf1fb3528c48f4125c6fae6a9ccba1
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94417580"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Интеграция Azure обозреватель данных с фабрикой данных Azure

[Фабрика данных Azure](/azure/data-factory/) (ADF) — это облачная служба интеграции данных, которая позволяет интегрировать различные хранилища данных и выполнять действия с данными. ADF позволяет создавать управляемые данными рабочие процессы для оркестрации и автоматизации перемещения и преобразования данных. Azure обозреватель данных — это одно из [поддерживаемых хранилищ данных](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) в фабрике данных Azure. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Действия фабрики данных Azure для Azure обозреватель данных

Для пользователей Azure обозреватель данных доступны различные интеграции с фабрикой данных Azure:

### <a name="copy-activity"></a>Действие копирования
 
Действие копирования фабрики данных Azure используется для перемещения данных между хранилищами данных. Azure обозреватель данных поддерживается в качестве источника, где данные копируются из обозреватель данных Azure в любое поддерживаемое хранилище данных, а также в приемник, где данные копируются из любого поддерживаемого хранилища данных в обозреватель данных Azure. Дополнительные сведения см. [в статье копирование данных в azure обозреватель данных или из нее с помощью фабрики данных Azure](/azure/data-factory/connector-azure-data-explorer). подробное пошаговое руководство см. в статье [Загрузка данных из фабрики данных Azure в обозреватель данных Azure](data-factory-load-data.md).
Azure обозреватель данных поддерживается Azure IR (Integration Runtime), который используется при копировании данных в Azure, и на локальных IR-дисках, используемых при копировании данных из хранилищ данных в локальную среду или в сети с управлением доступом, например в виртуальной сети Azure. Дополнительные сведения см. [в статье Выбор используемого IR-порта](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use) .
 
> [!TIP]
> При использовании действия копирования и создании **связанной службы** или **набора данных** выберите хранилище данных **Azure обозреватель данных (Kusto)** , а не старое хранилище данных **Kusto**.  

### <a name="lookup-activity"></a>Действие поиска
 
Действие поиска используется для исполнения запросов в Azure обозреватель данных. Результат запроса будет возвращен в качестве выходных данных действия поиска. его можно использовать в следующем действии конвейера, как описано в [документации по поиску ADF](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  

Помимо предельного размера ответа в 5 000 строк и 2 МБ, действие также имеет ограничение времени ожидания запроса, равное 1 часу.

### <a name="command-activity"></a>Действие команды

Действие команды разрешает выполнение [команд управления](kusto/concepts/index.md#control-commands)обозреватель данных Azure. В отличие от запросов, команды управления потенциально могут изменять данные или метаданные. Некоторые команды управления предназначены для приема данных в Azure обозреватель данных, с помощью таких команд, как `.ingest` или `.set-or-append` ) или копирования данных из обозреватель данных Azure во внешние хранилища данных с помощью таких команд, как `.export` .
Подробное пошаговое руководство по действию команды см. [в статье Использование действия команды фабрики данных Azure для запуска команд управления обозреватель данных Azure](data-factory-command-activity.md).  Использование команды управления для копирования данных иногда может быть более быстрым и дешевле, чем действие копирования. Чтобы определить, когда следует использовать действие команды в сравнении с действием копирования, см. раздел [Выбор между копированием и действиями команды при копировании данных](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Копирование из шаблона базы данных в оптовую копию

[Копирование из базы данных в Azure обозреватель данных с помощью шаблона фабрики данных](data-factory-template.md) Azure является стандартным конвейером фабрики данных Azure. Шаблон используется для создания множества конвейеров для каждой базы данных или каждой таблицы для ускорения копирования данных. 

### <a name="mapping-data-flows"></a>Сопоставление потоков данных 

[Потоки данных сопоставления фабрики данных Azure](/azure/data-factory/concepts-data-flow-overview) являются визуально спроектированными преобразованиями данных, которые позволяют инженерам по разработке данных разрабатывать логику преобразования графических данных без написания кода. Для создания потока данных и приема данных в Azure обозреватель данных используйте следующий метод:

1. Создайте [поток данных сопоставления](/azure/data-factory/data-flow-create).
1. [Экспортируйте данные в большой двоичный объект Azure](/azure/data-factory/data-flow-sink). 
1. Определите [сетку событий](ingest-data-event-grid.md) или [действие копирования ADF](data-factory-load-data.md) для приема данных в Azure обозреватель данных.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Выбор между копированием и действиями команды обозреватель данных Azure при копировании данных 

Этот раздел поможет вам выбрать правильные действия для копирования данных.

При копировании данных из или в обозреватель данных Azure доступны два варианта в фабрике данных Azure:
* Действие копирования.
* Действие команды Azure обозреватель данных, которое выполняет одну из команд управления для перемещения данных в обозреватель данных Azure. 

### <a name="copy-data-from-azure-data-explorer"></a>Копирование данных из Azure обозреватель данных
  
Данные можно скопировать из обозреватель данных Azure с помощью действия копирования или [`.export`](kusto/management/data-export/index.md) команды. `.export`Команда выполняет запрос, а затем экспортирует результаты запроса. 

Сравнение действия копирования и `.export` команды копирования данных из обозреватель данных Azure см. в следующей таблице.

| | Действие копирования | команда. Export |
|---|---|---|
| **Описание потока** | ADF выполняет запрос к Kusto, обрабатывает результат и отправляет его в целевое хранилище данных. <br>( **ADX > ADF > приемник данных** ) | ADF отправляет `.export` команду управления в Azure обозреватель данных, которая выполняет команду и отправляет данные непосредственно в целевое хранилище данных. <br>( **ADX > хранилище данных приемника** ) |
| **Поддерживаемые целевые хранилища данных** | Широкий спектр [поддерживаемых хранилищ данных](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, большой двоичный объект Azure, база данных SQL |
| **Производительность** | Централизованное | <ul><li>Распределенный (по умолчанию), параллельный экспорт данных из нескольких узлов</li><li>Повышение эффективности и COGS (стоимость проданных товаров).</li></ul> |
| **Ограничения сервера** | [Ограничения запросов](kusto/concepts/querylimits.md) могут быть расширены или отключены. По умолчанию запросы ADF содержат: <ul><li>Предельный размер 500 000 записей или 64 МБ.</li><li>Ограничение времени, равное 10 минутам.</li><li>`noTruncation` Задайте значение false.</li></ul> | По умолчанию расширяет или отключает ограничения запросов. <ul><li>Ограничения размера отключены.</li><li>Время ожидания сервера увеличивается до 1 часа.</li><li>`MaxMemoryConsumptionPerIterator` и `MaxMemoryConsumptionPerQueryPerNode` расширяются до максимума (5 ГБ, тоталфисикалмемори/2).</li></ul>

> [!TIP]
> Если целевой копией является одно из хранилищ данных `.export` , поддерживаемое командой, и если ни одно из функций действия копирования не является важным для ваших нужд, выберите `.export` команду.

### <a name="copying-data-to-azure-data-explorer"></a>Копирование данных в Azure обозреватель данных

Данные можно скопировать в обозреватель данных Azure с помощью действия копирования или команд приема, таких как прием [от запроса](kusto/management/data-ingestion/ingest-from-query.md) (,,, `.set-or-append` `.set-or-replace` и прием `.set` `.replace)` [из хранилища](kusto/management/data-ingestion/ingest-from-storage.md) ( `.ingest` ). 

Сравнение действия копирования и команды приема для копирования данных в Azure обозреватель данных см. в следующей таблице.

| | Действие копирования | Прием из запроса<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Прием из хранилища <br> `.ingest` |
|---|---|---|---|
| **Описание потока** | ADF получает данные из исходного хранилища данных, преобразует их в табличный формат и выполняет необходимые изменения сопоставления схемы. Затем ADF отправляет данные в большие двоичные объекты Azure, разделяет их на блоки, а затем скачивает большие двоичные объекты, чтобы принимать их в таблицу ADX. <br> ( **Исходное хранилище данных > ADF > больших двоичных объектов Azure > ADX** ) | Эти команды могут выполнить запрос или `.show` команду и принять результаты запроса в таблицу ( **ADX > ADX** ). | Эта команда принимает данные в таблицу, получая данные из одного или нескольких артефактов облачного хранилища. |
| **Поддерживаемые хранилища исходных данных** |  [разнообразные параметры](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, большой двоичный объект Azure, SQL (с помощью [подключаемого модуля sql_request ()](kusto/query/sqlrequestplugin.md)), Cosmos (с помощью [подключаемого модуля cosmosdb_sql_request](kusto\query\mysqlrequest-plugin.md)) и любое другое хранилище данных, которое предоставляет API-интерфейсы HTTP или Python. | Файловая система, хранилище BLOB-объектов Azure, ADLS Gen 1, ADLS Gen 2 |
| **Производительность** | Приемы направляются в очередь и управляются, что гарантирует принятие малых размеров и обеспечивает высокий уровень доступности за счет балансировки нагрузки, повторных попыток и обработки ошибок. | <ul><li>Эти команды не предназначались для импорта больших объемов данных.</li><li>Работает как ожидается и дешевле. Но в рабочих сценариях и при большом объеме трафика и размерах данных используйте действие копирования.</li></ul> |
| **Ограничения сервера** | <ul><li>Без ограничения размера.</li><li>Максимальное время ожидания: 1 час на принимающий большой двоичный объект. |<ul><li>Существует только ограничение размера для части запроса, которое можно пропустить, указав `noTruncation=true` .</li><li>Максимальное время ожидания: 1 час.</li></ul> | <ul><li>Без ограничения размера.</li><li>Максимальное время ожидания: 1 час.</li></ul>|

> [!TIP]
> * При копировании данных из ADF в Azure обозреватель данных использовать `ingest from query` команды.  
> * Для больших наборов данных (>1 ГБ) используйте действие копирования.  

## <a name="required-permissions"></a>Необходимые разрешения

В следующей таблице перечислены необходимые разрешения для различных шагов интеграции с фабрикой данных Azure.

| Шаг | Операция | Минимальный уровень разрешений | Примечания |
|---|---|---|---|
| **Создание связанной службы** | Навигация по базам данных | *средство просмотра баз данных* <br>Вошедший в систему пользователь, использующий ADF, должен иметь право на чтение метаданных базы данных. | Пользователь может указать имя базы данных вручную. |
| | Проверить подключение | *монитор базы данных* или *принимающий таблицы* <br>Субъект-служба должен быть разрешен для выполнения команд уровня базы данных `.show` или приема на уровне таблицы. | <ul><li>TestConnection проверяет подключение к кластеру, а не к базе данных. Она может быть выполнена, даже если база данных не существует.</li><li>Разрешения администратора таблиц недостаточно.</li></ul>|
| **Создание набора данных** | Навигация по таблицам | *монитор базы данных* <br>Пользователь, выполнивший вход в систему с помощью ADF, должен иметь права на выполнение команд уровня базы данных `.show` . | Пользователь может предоставить имя таблицы вручную.|
| **Создание набора данных** или **действия копирования** | Просмотр данных | *средство просмотра баз данных* <br>Субъект-служба должен иметь права на чтение метаданных базы данных. | | 
|   | Импорт схемы | *средство просмотра баз данных* <br>Субъект-служба должен иметь права на чтение метаданных базы данных. | Если ADX является источником для копирования из табличного в табличное, ADF импортирует схему автоматически, даже если пользователь не импортировал схему явным образом. |
| **ADX в качестве приемника** | Создание сопоставления столбцов по имени | *монитор базы данных* <br>Субъект-служба должен быть разрешен для выполнения команд уровня базы данных `.show` . | <ul><li>Все обязательные операции будут работать со службой *приема таблиц*.</li><li> Некоторые необязательные операции могут завершиться ошибкой.</li></ul> |
|   | <ul><li>Создание сопоставления CSV-файла для таблицы</li><li>Удалить сопоставление</li></ul>| получение *таблиц* или *администратор базы данных* <br>Субъект-служба должен быть полномочным для внесения изменений в таблицу. | |
|   | Прием данных | получение *таблиц* или *администратор базы данных* <br>Субъект-служба должен быть полномочным для внесения изменений в таблицу. | | 
| **ADX в качестве источника** | Выполнение запроса | *средство просмотра баз данных* <br>Субъект-служба должен иметь права на чтение метаданных базы данных. | |
| **Команда Kusto** | | В соответствии с уровнем разрешений каждой команды. |

## <a name="performance"></a>Производительность 

Если Azure обозреватель данных является источником и вы используете действие подстановки, копирования или команды, содержащее запрос, где, см. [рекомендации по запросам](kusto/query/best-practices.md) для получения сведений о производительности и [документации ADF для действия копирования](/azure/data-factory/copy-activity-performance).
  
В этом разделе рассматривается использование действия копирования, в котором обозреватель данных Azure является приемником. Оценка пропускной способности для приемника обозреватель данных Azure составляет 11-13 Мбит/с. В следующей таблице описаны параметры, влияющие на производительность приемника обозреватель данных Azure.

| Параметр | Примечания |
|---|---|
| **Близкое к географическому компоненту** | Разместите все компоненты в одном регионе:<ul><li>хранилища данных источника и приемника.</li><li>Среда выполнения интеграции ADF.</li><li>Ваш кластер ADX.</li></ul>Убедитесь, что по крайней мере ваша среда выполнения интеграции находится в том же регионе, что и кластер ADX. |
| **Число диус** | 1 виртуальная машина для каждых 4 диус, используемых ADF. <br>Увеличение диус поможет вам только в том случае, если источником является хранилище на основе файлов с несколькими файлами. Каждая виртуальная машина будет обрабатывать отдельный файл параллельно. Таким образом, копирование одного большого файла будет иметь большую задержку, чем копирование нескольких файлов меньшего размера.|
|**Сумма и номер SKU кластера ADX** | Большое число узлов ADX повышает время обработки приема.|
| **Parallelism** |    Чтобы скопировать очень большой объем данных из базы данных, создайте разделы данных, а затем используйте цикл ForEach, который копирует каждую секцию параллельно или используйте [шаблон обозреватель данных "групповое копирование из базы данных в Azure](data-factory-template.md)". Примечание. **Параметры**  >  **степени параллелизма** в действии копирования не относятся к ADX. |
| **Сложность обработки данных** | Задержка зависит от формата файла исходного кода, сопоставления столбцов и сжатия.|
| **Виртуальная машина, на которой запущена среда выполнения интеграции** | <ul><li>Для копии Azure нельзя изменить виртуальные машины ADF и номера SKU компьютеров.</li><li> Для копирования из локальной среды в Azure необходимо убедиться, что виртуальная машина, на которой размещена локальная среда IR, достаточно надежна.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Советы и распространенные ошибки

### <a name="monitor-activity-progress"></a>Мониторинг хода выполнения действий

* При наблюдении за ходом выполнения действия *записанное свойство данных* может значительно превышать свойство *Read* , так как *считывание данных* производится в соответствии с размером двоичного файла, а *данные, записываемые* , рассчитываются в соответствии с размером в памяти, после десериализации и распаковки данных.

* При наблюдении за ходом выполнения действия вы видите, что данные записываются в приемник обозреватель данных Azure. При запросе к таблице Azure обозреватель данных вы видите, что данные не получены. Это связано с тем, что при копировании в Azure обозреватель данных необходимо выполнить два этапа. 
    * Первый этап считывает исходные данные, разделяет их на фрагменты размером 900 МБ и передает каждый блок в большой двоичный объект Azure. Первый этап отображается в представлении "ход выполнения действий ADF". 
    * Второй этап начинается после передачи всех данных в большие двоичные объекты Azure. Узлы обозреватель данных подсистемы Azure загружают большие двоичные объекты и принимают данные в таблицу приемника. Затем данные отображаются в таблице Azure обозреватель данных.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Не удалось принять файлы CSV из-за неправильного экранирования

Обозреватель данных Azure ждет, что CSV-файлы будут согласованы с [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Она ждет:
* Поля, содержащие символы, требующие экранирования (например, "и новые строки), должны начинаться и заканчиваться символом" без пробелов **"** . Все символы *в* поле преобразуются в escape **-знаки с помощью** двойного **символа (** " **"** ). Например, _"Hello," World "" "_ — это ДОПУСТИМЫй CSV-файл с единственной записью, имеющей один столбец или поле с содержимым _Hello, «World»_.
* Все записи в файле должны иметь одинаковое число столбцов и полей.

Фабрика данных Azure допускает обратную косую черту (escape-символ). Если создать CSV-файл с символом обратной косой черты с помощью фабрики данных Azure, то при приеме файла в Azure обозреватель данных произойдет сбой.

#### <a name="example"></a>Пример

Следующие текстовые значения: Hello, World<br/>
ABC DEF<br/>
EF "АБК\Д"<br/>
"ABC DEF<br/>

Должен появиться в правильном CSV-файле следующим образом: "Hello," "World" "<br/>
"ABC DEF"<br/>
"" ABC DEF "<br/>
"" "АБК\Д" "EF"<br/>

Если использовать escape-символ по умолчанию (обратная косая черта), следующий CSV не будет работать с Azure обозреватель данных: "Hello, \" World \" "<br/>
"ABC DEF"<br/>
" \" abc def"<br/>
" \" Абк\д \" EF"<br/>

### <a name="nested-json-objects"></a>Вложенные объекты JSON

При копировании JSON-файла в обозреватель данных Azure Обратите внимание на следующее:
* Массивы не поддерживаются.
* Если структура JSON содержит типы данных объектов, то фабрика данных Azure будет сводить дочерние элементы объекта и пытаться сопоставлять каждый дочерний элемент с другим столбцом в таблице обозреватель данных Azure. Если требуется, чтобы весь элемент объекта был сопоставлен с одним столбцом в обозреватель данных Azure, сделайте следующее:
    * Прием всей строки JSON в один динамический столбец в обозреватель данных Azure.
    * Вручную измените определение конвейера с помощью редактора JSON фабрики данных Azure. В **сопоставлениях**
       * Удалите несколько сопоставлений, созданных для каждого дочернего элемента, и добавьте одно сопоставление, которое сопоставляет тип объекта со столбцом таблицы.
       * После закрывающей квадратной скобки добавьте запятую, а затем:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Укажите AdditionalProperties при копировании в Azure обозреватель данных

> [!NOTE]
> В настоящее время эта функция доступна путем изменения полезных данных JSON вручную. 

Добавьте одну строку в раздел "Sink" действия копирования, как показано ниже.

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Экранирование значения может быть непростой задачей. Используйте следующий фрагмент кода в качестве ссылки:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

Напечатанное значение:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [Копировать данные в azure обозреватель данных с помощью фабрики данных Azure](data-factory-load-data.md).
* Узнайте, как использовать [шаблон фабрики данных Azure для копирования из базы данных в Azure обозреватель данных](data-factory-template.md).
* Сведения об использовании [действия команды фабрики данных Azure для запуска команд управления обозреватель данных Azure](data-factory-command-activity.md).
* Дополнительные сведения о запросах [Обозреватель данных Azure](web-query-data.md) для запросов к данным.



