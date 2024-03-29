---
title: Очистка данных в Azure обозреватель данных
description: В этой статье описывается очистка данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 05/12/2020
ms.openlocfilehash: b4e65fd2ca01f5a2de0a8f703e1b91f0d3722f92
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324725"
---
# <a name="data-purge"></a>Очистка данных

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

В качестве платформы данных Azure обозреватель данных поддерживает возможность удаления отдельных записей с помощью Kusto `.purge` и связанных команд. Можно также [очистить всю таблицу](#purging-an-entire-table).  

> [!WARNING]
> Удаление данных с помощью `.purge` команды предназначено для защиты персональных данных и не должно использоваться в других сценариях. Он не предназначен для частого выполнения запросов на удаление или удаления больших объемов данных и может существенно повлиять на производительность службы.

## <a name="purge-guidelines"></a>Инструкции по очистке

Тщательно Разработайте схему данных и изучите соответствующие политики, прежде чем хранить персональные данные в Azure обозреватель данных.

1. В лучшем случае срок хранения этих данных достаточно короткий и данные автоматически удаляются.
1. Если использование срока хранения невозможно, изолируйте все данные, которые подчиняются правилам конфиденциальности, в небольшом количестве таблиц Azure обозреватель данных. Оптимально используйте только одну таблицу и свяжите ее со всеми другими таблицами. Такая изоляция позволяет запускать [процесс очистки](#purge-process) данных в небольшом количестве таблиц, содержащих конфиденциальные данные, и избегать всех других таблиц.
1. Вызывающая сторона должна выполнять каждую попытку пакетной обработки `.purge` команд до 1-2 команд на таблицу в день. Не выпускайте несколько команд с уникальными предикатами удостоверения пользователя. Вместо этого отправьте одну команду, предикат которой содержит все удостоверения пользователей, требующие очистки.

## <a name="purge-process"></a>Процесс очистки

Процесс выборочной очистки данных из обозреватель данных Azure происходит в следующих шагах:

1. Этап 1. предоставление входных данных с именем таблицы Azure обозреватель данных и предикатом записи, указывающей, какие записи нужно удалить. Kusto сканирует таблицу, чтобы найти экстенты данных, которые будут участвовать в очистке данных. Выявленные экстенты имеют одну или несколько записей, для которых предикат возвращает значение true.
1. Этап 2. (обратимое удаление) замените каждый экстент данных в таблице (определенный на шаге (1)) на восстановленную версию. В возвращенной версии не должно быть записей, для которых предикат возвращает значение true. Если новые данные не поступают в таблицу, то в конце этого этапа запросы больше не будут возвращать данные, для которых предикат возвращает значение true. Длительность фазы обратимого удаления зависит от следующих параметров: 
     * Число записей, которые необходимо очистить 
     * Распределение записей по экстентам данных в кластере 
     * Число узлов в кластере  
     * Емкость запасных ресурсов для операций очистки
     * Некоторые другие факторы
     
    Продолжительность фазы 2 может варьироваться от нескольких секунд до нескольких часов.
1. Этап 3. (жесткое удаление) выполните откат всех артефактов хранилища, которые могут иметь "подозрительные" данные, и удалите их из хранилища. Этот этап выполняется по крайней мере через пять дней после завершения предыдущего этапа, но не более чем за 30 дней после начальной команды. Эти временные шкалы настроены на соблюдение требований к конфиденциальности данных.

`.purge`Выполнение команды запускает этот процесс, что займет несколько дней. Если плотность записей, для которой применяется предикат, достаточно велика, процесс будет эффективно воспринимать все данные в таблице. Это значительно влияет на производительность и COGS (стоимость проданных товаров).

## <a name="purge-limitations-and-considerations"></a>Удаление ограничений и рекомендаций

* Процесс очистки является окончательным и необратимым. Отменить этот процесс или восстановить данные, которые были очищены, невозможно. Команды, такие как [Отменить удаление таблицы](../management/undo-drop-table-command.md) , не могут восстанавливать очищенные данные. Откат данных до предыдущей версии не может пройти до последней команды очистки.

* Перед выполнением очистки проверьте предикат, выполнив запрос и убедившись, что результаты соответствуют ожидаемому результату. Можно также использовать двухэтапный процесс, возвращающий ожидаемое количество записей, которые будут очищены. 

* `.purge`Команда выполняется для конечной точки управление данными: `https://ingest-[YourClusterName].[region].kusto.windows.net` .
   Команде требуются разрешения [администратора базы данных](../management/access-control/role-based-authorization.md) для соответствующих баз данных. 
* Из-за влияния очистки на производительность процесса и гарантии того, что были выполнены  [инструкции по очистке](#purge-guidelines) , вызывающий объект должен изменить схему данных таким образом, чтобы минимальное число таблиц включало в себя важные данные, и команды пакетной службы для таблицы, чтобы уменьшить ЗНАЧИТЕЛЬНЫЙ эффект cogs от процесса очистки.
* Параметр `predicate` команды [.purge](#purge-table-tablename-records-command) позволяет указать записи для очистки.
`Predicate` размер ограничен 63 КБ. При создании `predicate` :
    * Используйте [оператор in](../query/inoperator.md), например `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')` . 
    * Обратите внимание на ограничения [оператора in](../query/inoperator.md) (список может содержать до `1,000,000` значений).
    * Если размер запроса большой, используйте [ `externaldata` оператор](../query/externaldata-operator.md), например `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` . В файле хранится список идентификаторов для очистки.
    * Общий размер запроса после расширения всех `externaldata` больших двоичных объектов (общего размера всех больших двоичных объектов) не может превышать 64 МБ. 

## <a name="purge-performance"></a>Очистка производительности

В любой момент времени в кластере может выполняться только один запрос на очистку. Все остальные запросы помещаются в очередь в `Scheduled` состоянии. Отслеживайте размер очереди запросов очистки и следите за соответствующими ограничениями, соответствующими требованиям к данным.

Чтобы сократить время выполнения очистки:
* Следуйте [указаниям по очистке](#purge-guidelines) , чтобы уменьшить объем очищенных данных.
* Настройте [политику кэширования](../management/cachepolicy.md) , так как очистка занимает больше времени на холодных данных.
* Масштабирование кластера

* Увеличьте емкость очистки кластера после тщательного рассмотрения, как описано в [экстентах очистка перестроения емкости](../management/capacitypolicy.md#extents-purge-rebuild-capacity). Для изменения этого параметра требуется открыть запрос в [службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="trigger-the-purge-process"></a>Активация процесса очистки

> [!NOTE]
> Выполнение очистки вызывается путем выполнения команды [Очистить таблицу *TableName* records](#purge-table-tablename-records-command) в управление данными конечной точке https://ingest- [йоурклустернаме]. [ Регион]. kusto. Windows. NET.

### <a name="purge-table-tablename-records-command"></a>Команда очистки записей таблицы *TableName*

Команда очистки может быть вызвана двумя способами для различения сценариев использования:

* Программный вызов: один шаг, который должен вызываться приложениями. Вызов этой команды напрямую запускает последовательность выполнения очистки.

  **Синтаксис**

  ```kusto
  // Connect to the Data Management service
  #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
 
  .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
   ```

* Вызов человеком: двухэтапный процесс, который требует явного подтверждения в качестве отдельного шага. При первом вызове команды возвращается токен проверки, который должен быть предоставлен для выполнения фактической очистки. Эта последовательность снижает риск непреднамеренного удаления неверных данных.

 > [!NOTE]
 > Для первого шага, выполняемого в двух шагах, требуется выполнение запроса ко всему набору данных, чтобы определить записи для очистки.
 > Этот запрос может истекает или отдать сбой в больших таблицах, особенно с большим количеством данных холодного кэша. В случае сбоев следует проверить предикат самостоятельно, а после проверки правильности использовать одношаговую очистку с `noregrets` параметром.

**Синтаксис**

  ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
  ```
    
| Параметры  | Описание  |
|---------|---------|
| `DatabaseName`   |   Имя базы данных.      |
| `TableName`     |     Имя таблицы    |
| `Predicate`    |    Определяет записи для очистки. См. раздел ограничения предикатов очистки ниже. | 
| `noregrets`    |     Если этот параметр задан, активируется Одношаговая активация.    |
| `verificationtoken`     |  В сценарии двухфакторной активации ( `noregrets` не задано) этот токен можно использовать для выполнения второго шага и фиксации действия. Если `verificationtoken` параметр не указан, запускается первый шаг команды. Сведения об очистке будут возвращены с маркером, который необходимо передать обратно в команду, чтобы выполнить шаг #2.   |

**Ограничения на очистку предикатов**

* Предикат должен быть простым выделением (например, *WHERE [ColumnName] = = ' x '*  /  *, где [ColumnName] в (' x ', ' Y ', ' Z ') и [осерколумн] = = ' a '*).
* Несколько фильтров должны сочетаться с "and", а не отдельными `where` предложениями (например, `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` без `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'` ).
* Предикат не может ссылаться на таблицы, отличные от очищенной таблицы (*TableName*). Предикат может содержать только инструкцию Selection ( `where` ). Он не может проецировать определенные столбцы из таблицы (выходная схема при выполнении "*`table` | Предикат*"должен соответствовать схеме таблицы).
* Системные функции (такие как, `ingestion_time()` , `extent_id()` ) не поддерживаются.

#### <a name="example-two-step-purge"></a>Пример: два этапа очистки

Чтобы начать очистку сценария двухфакторной активации, выполните шаг #1 команды:

 ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
 ```

**Выходные данные**

 | нумрекордстопурже | естиматедпуржеексекутионтиме| верификатионтокен
 |---|---|---
 | 1 596 | 00:00:02 | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

Затем проверьте Нумрекордстопурже перед выполнением шага #2. 

Чтобы выполнить очистку в сценарии с двумя шагами активации, используйте токен проверки, возвращенный на шаге #1, для выполнения шага #2.

```kusto
.purge table MyTable records in database MyDatabase
 with(verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
<| where CustomerId in ('X', 'Y')
```

**Выходные данные**

| `OperationId` | `DatabaseName` | `TableName`|`ScheduledTime` | `Duration` | `LastUpdatedOn` |`EngineOperationId` | `State` | `StateDetails` |`EngineStartTime` | `EngineDuration` | `Retries` |`ClientRequestId` | `Principal`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Запланировано | | | |0 |KE. Рункомманд; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |Идентификатор приложения AAD =...|

#### <a name="example-single-step-purge"></a>Пример: Одношаговая очистка

Чтобы активировать очистку в сценарии одношаговой активации, выполните следующую команду:

```kusto
// Connect to the Data Management service
 #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
 
.purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
```

**Выходные данные**

| `OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Запланировано | | | |0 |KE. Рункомманд; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |Идентификатор приложения AAD =...|

### <a name="cancel-purge-operation-command"></a>Команда отмены операции очистки

При необходимости можно отменить отложенные запросы на очистку.

> [!NOTE]
> Эта операция предназначена для сценариев восстановления с ошибками. Она не гарантируется успешно и не должна быть частью обычной операционной последовательности. Его можно применить только к запросам в очереди (еще не переданным на узел ядра для выполнения). Команда выполняется в конечной точке Управление данными.

**Синтаксис**

```kusto
 .cancel purge <OperationId>
```

**Пример**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
```

**Выходные данные**

Выходные данные этой команды совпадают с выходными данными команды "Показать очистки с *идентификатором",* в которых отображается обновленное состояние отмененной операции очистки. Если попытка прошла успешно, состояние операции изменится на `Abandoned` . В противном случае состояние операции не изменяется. 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandoned | | | |0 |KE. Рункомманд; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |Идентификатор приложения AAD =...

## <a name="track-purge-operation-status"></a>Отслеживание состояния операции очистки 

> [!NOTE]
> Операции очистки можно отслеживанию с помощью команды " [отобразить очистки](#show-purges-command) ", выполняемой для управление данными конечной точки https://ingest- [йоурклустернаме]. [ регион]. kusto. Windows. NET.

Status = "Completed" указывает на успешное завершение первого этапа операции очистки, т. е. записи обратимо удалены и больше не доступны для запросов. Клиенты не должны отследить и проверить завершение второго этапа (жесткое удаление). Этот этап отслеживается на внутреннем уровне обозреватель данных Azure.

### <a name="show-purges-command"></a>Отображать команду "очистки"

`Show purges` команда отображает состояние операции очистки, указывая идентификатор операции в течение запрошенного периода времени. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

| Элемент Property  |Описание  |Обязательный или необязательный|
|---------|------------|-------|
|`OperationId `   |      ИДЕНТИФИКАТОР операции Управление данными, выводимый после выполнения одного этапа или второго этапа.   |Обязательный
|`StartDate`    |   Нижний предел времени для операций фильтрации. Если этот параметр опущен, по умолчанию используется значение 24 часа до текущего времени.      |Необязательно
|`EndDate`    |  Верхний предел времени для операций фильтрации. Если этот параметр опущен, по умолчанию используется текущее время.       |Необязательно
|`DatabaseName`    |     Имя базы данных для фильтрации результатов.    |Необязательно

> [!NOTE]
> Состояние будет указано только для баз данных, у которых у клиента есть разрешения [администратора базы данных](../management/access-control/role-based-authorization.md) .

**Примеры**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**Выходные данные** 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |MyDatabase |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Завершено |Очистка успешно завершена (Ожидающие удаления артефактов хранилища) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |KE. Рункомманд; 1d0ad28b-f791-4f5a-a60f-0e32318367b7 |Идентификатор приложения AAD =...

* `OperationId` — ИДЕНТИФИКАТОР операции DM, возвращенный при выполнении очистки. 
* `DatabaseName`* * — имя базы данных (с учетом регистра). 
* `TableName` — имя таблицы (с учетом регистра). 
* `ScheduledTime` -время выполнения команды очистки для службы DM. 
* `Duration` — Общая длительность операции очистки, включая время ожидания выполнения очереди DM. 
* `EngineOperationId` — ИДЕНТИФИКАТОР операции фактической очистки, выполняемой в ядре. 
* `State` -Очистка состояния может принимать одно из следующих значений: 
    * `Scheduled` -операция очистки запланирована к выполнению. Если задание по-прежнему запланировано, возможно, есть невыполненные операции очистки. Чтобы очистить эту невыполненную работу, см. статью [Очистка производительности](#purge-performance) . Если операция очистки завершается неудачей во временной ошибке, она будет повторно выполнена с помощью DM и снова задается в расписании (так что вы можете увидеть переход от запланированного выполнения операции к выполнению и назад к запланированному).
    * `InProgress` — операция очистки выполняется в подсистеме. 
    * `Completed` -Очистка успешно завершена.
    * `BadInput` -не удалось выполнить очистку для неверных входных данных. Повторная попытка не будет выполнена. Эта ошибка может быть вызвана различными проблемами, такими как синтаксическая ошибка в предикате, недопустимый предикат для команд очистки, запрос, превышающий предел (например, более 1 MБ сущностей в `externaldata` операторе или более 64 МБ общего размера расширенного запроса), а также ошибки 404 или 403 для `externaldata` больших двоичных объектов.
    * `Failed` -не удалось выполнить очистку, повторная попытка не будет выполнена. Эта ошибка может произойти, если операция ожидала слишком длинную очередь (более 14 дней) из-за невыполненной работы других операций очистки или ряда сбоев, превышающих предельное число повторных попыток. Последний вызовет внутреннее оповещение мониторинга и будет исследовано командой Azure обозреватель данных. 
* `StateDetails` — Описание состояния.
* `EngineStartTime` — время выдачи команды обработчику. Если между этим временем и Счедуледтиме возникает большая разница, обычно существует значительная невыполненная работа, и кластер не работает с максимальной скоростью. 
* `EngineDuration` — время фактического выполнения очистки в ядре. Если очистка была повторена несколько раз, это сумма всех длительностей выполнения. 
* `Retries` — Количество повторных попыток выполнения операции службой DM из-за временной ошибки.
* `ClientRequestId` — Идентификатор действия клиента для запроса на очистку DM. 
* `Principal` — Идентификатор издателя команды очистки.

## <a name="purging-an-entire-table"></a>Очистка всей таблицы

Очистка таблицы включает удаление таблицы и пометку ее как очищенную, чтобы процесс жесткого удаления, описанный в [процессе очистки](#purge-process) , выполнялся на нем. Удаление таблицы без очистки не приводит к удалению всех ее артефактов хранилища. Эти артефакты удаляются в соответствии с политикой жесткого хранения, изначально установленной для таблицы. `purge table allrecords`Команда выполняется быстро и эффективно и является предпочтительной для процесса очистки записей, если это применимо для вашего сценария. 

> [!NOTE]
> Команда вызывается путем выполнения команды " [Очистить таблицу *TableName* аллрекордс](#purge-table-tablename-allrecords-command) " в конечной точке управление данными https://ingest- [йоурклустернаме]. [ регион]. kusto. Windows. NET.

### <a name="purge-table-tablename-allrecords-command"></a>Команда "очистить таблицу *TableName* аллрекордс"

Аналогично команде "[. Очистка записей таблицы ](#purge-table-tablename-records-command)" Эта команда может быть вызвана программно (одношаговым) или в ручном режиме (в два этапа).

1. Программный вызов (один шаг):

     **Синтаксис**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

1. Вызов пользователей (два этапа):

     **Синтаксис**

     ```kusto
   
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)

     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    | Параметры  |Описание  |
    |---------|---------|
    | `DatabaseName`   |   Имя базы данных.      |
    | `TableName`    |     Имя таблицы.    |
    | `noregrets`    |     Если этот параметр задан, активируется Одношаговая активация.    |
    | `verificationtoken`     |  В сценарии двухфакторной активации ( `noregrets` не задано) этот токен можно использовать для выполнения второго шага и фиксации действия. Если `verificationtoken` параметр не указан, запускается первый шаг команды. На этом шаге возвращается маркер, который передается обратно в команду и выполняется шаг #2.|

#### <a name="example-two-step-purge"></a>Пример: два этапа очистки

1. Чтобы начать очистку сценария двухфакторной активации, выполните шаг #1 команды: 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **Выходные данные**

    | `VerificationToken`|
    |---|
    | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b|

1.  Чтобы выполнить очистку в сценарии с двумя шагами активации, используйте токен проверки, возвращенный на шаге #1, для выполнения шага #2.

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    Выходные данные совпадают с выходными данными команды ". показывать таблицы" (возвращается без очищенной таблицы).

    **Выходные данные**

    |  TableName|имя_базы_данных|Папка|DocString
    |---|---|---|---
    |  осертабле|MyDatabase|---|---


#### <a name="example-single-step-purge"></a>Пример: Одношаговая очистка

Чтобы активировать очистку в сценарии одношаговой активации, выполните следующую команду:

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

Выходные данные совпадают с выходными данными команды ". показывать таблицы" (возвращается без очищенной таблицы).

**Выходные данные**

|TableName|имя_базы_данных|Папка|DocString
|---|---|---|---
|осертабле|MyDatabase|---|---

