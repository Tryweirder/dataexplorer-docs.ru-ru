---
title: безопасность на уровне строк Azure обозреватель данных
description: В этой статье описывается безопасность на уровне строк в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/11/2020
ms.openlocfilehash: 2b81319740143a1c0ebc31c4a9b93d7b3b2e5223
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941814"
---
# <a name="row-level-security"></a>Безопасность на уровне строк

Используйте членство в группе или контекст выполнения для управления доступом к строкам в таблице базы данных.

Безопасность на уровне строк (RLS) упрощает проектирование и кодирование безопасности. Это позволяет применять ограничения доступа к строкам данных в приложении. Например, ограничьте доступ пользователей к строкам, относящимися к их Отделу, или ограничьте доступ клиентов только к тем данным, которые относятся к их компании.

Логика ограничения доступа расположена на уровне базы данных, а не на основе данных на другом уровне приложения. Система базы данных применяет ограничения доступа при каждом попыток доступа к данным с любого уровня. Эта логика делает систему безопасности более надежной и надежной, уменьшая контактную зону системы безопасности.

RLS позволяет предоставлять доступ другим приложениям и пользователям только к определенной части таблицы. Например, можно сделать следующее:

* Предоставление доступа только строкам, удовлетворяющим определенным условиям
* Анонимизация данные в некоторых столбцах
* Все вышеперечисленное

Дополнительные сведения см. [в разделе Управляющие команды для управления политикой безопасность на уровне строк](../management/row-level-security-policy.md).

> [!TIP]
> Эти функции часто используются для row_level_securityных запросов:
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

## <a name="limitations"></a>Ограничения

Количество таблиц, в которых можно настроить политику безопасность на уровне строк, не ограничено.

Политика RLS не может быть включена для таблицы:
* , для которого настроена [Непрерывная экспорт данных](../management/data-export/continuous-data-export.md) .
* Ссылка на запрос [политики обновления](./updatepolicy.md).
* для которой настроена [политика ограниченного доступа на просмотр](./restrictedviewaccesspolicy.md) .

## <a name="examples"></a>Примеры

### <a name="limit-access-to-sales-table"></a>Ограничение доступа к таблице продаж

В таблице с именем `Sales` каждая строка содержит сведения о продаже. Один из столбцов содержит имя продавца. Вместо того чтобы предоставлять сотрудникам доступ ко всем записям в `Sales` , включите в этой таблице политику безопасность на уровне строк, чтобы возвращались только те записи, в которых продавец является текущим пользователем.

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

Вы также можете маскировать номер кредитной карты:

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

Если требуется, чтобы каждый менеджер по продажам мог видеть все продажи определенной страны, можно определить запрос, аналогичный следующему:

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

Если у вас есть группа, содержащая руководителей, может потребоваться предоставить им доступ ко всем строкам. Запросите политику безопасность на уровне строк.

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

### <a name="expose-different-data-to-members-of-different-azure-ad-groups"></a>Предоставление различных данных членам разных групп Azure AD

Если у вас несколько групп Azure AD и вы хотите, чтобы участники каждой группы могли видеть другое подмножество данных, используйте эту структуру для запроса RLS. Предположим, что пользователь может принадлежать только к одной группе Azure AD.

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

### <a name="apply-the-same-rls-function-on-multiple-tables"></a>Применение одной и той же функции RLS к нескольким таблицам

Сначала определите функцию, которая получает имя таблицы в виде строкового параметра, и ссылается на таблицу с помощью `table()` оператора. 

Пример:

```kusto
.create-or-alter function RLSForCustomersTables(TableName: string) {
    table(TableName)
    | ...
}
```

Затем настройте RLS для нескольких таблиц таким образом:


```kusto
.alter table Customers1 policy row_level_security enable "RLSForCustomersTables('Customers1')"
.alter table Customers2 policy row_level_security enable "RLSForCustomersTables('Customers2')"
.alter table Customers3 policy row_level_security enable "RLSForCustomersTables('Customers3')"
```

### <a name="produce-an-error-upon-unauthorized-access"></a>Выдавать ошибку при несанкционированном доступе

Если требуется, чтобы несанкционированные пользователи таблиц получали ошибку вместо возврата пустой таблицы, используйте [`assert()`](../query/assert-function.md) функцию. В следующем примере показано, как создать эту ошибку в функции RLS:

```kusto
.create-or-alter function RLSForCustomersTables() {
    MyTable
    | where assert(current_principal_is_member_of('aadgroup=mygroup@mycompany.com') == true, "You don't have access")
}
```

Этот подход можно сочетать с другими примерами. Например, можно отобразить разные результаты для пользователей в разных группах AAD и создать ошибку для всех остальных.

### <a name="control-permissions-on-follower-databases"></a>Управление разрешениями для баз данных следующих элементов

Политика RLS, настроенная в рабочей базе данных, также вступит в силу в базах данных следующих. В рабочих и последующих базах данных нельзя настроить разные политики RLS. Однако [`current_cluster_endpoint()`](../query/current-cluster-endpoint-function.md) функцию в запросе RLS можно использовать для достижения того же результата, что и в таблицах-следах, имеющих разные запросы RLS.

Пример:

```kusto
.create-or-alter function RLSForCustomersTables() {
    let IsProductionCluster = current_cluster_endpoint() == "mycluster.eastus.kusto.windows.net";
    let DataForProductionCluster = TempTable | where IsProductionCluster;
    let DataForFollowerClusters = TempTable | where not(IsProductionCluster) | extend CreditCardNumber = "****";
    union DataForProductionCluster, DataForFollowerClusters
}
```

## <a name="more-use-cases"></a>Дополнительные варианты использования

* Сотрудник службы поддержки центра обработки вызовов может выявление абонентов по нескольким цифрам номера социального страхования или номера кредитной карты. Эти номера не должны быть полностью предоставлены специалисту службы поддержки. К таблице можно применить политику RLS, чтобы маскировать все последние четыре цифры любого номера социального страхования или кредитной карты в результирующем наборе любого запроса.
* Установите политику RLS, которая маскирует персональные данные (PII) и позволяет разработчикам запрашивать в рабочей среде возможность устранения неполадок без нарушения нормативных требований.
* В больницы можно задать политику RLS, которая позволяет медсестрам просматривать строки данных только для пациентов.
* Банк может установить политику RLS, чтобы ограничить доступ к строкам финансовых данных на основе бизнес-подразделения или роли сотрудника.
* Приложение с несколькими клиентами может хранить данные из многих клиентов в одном наборе таблиц (что является эффективным). Они будут использовать политику RLS для принудительного разделения строк данных каждого клиента из строк всех остальных клиентов, поэтому каждый клиент может видеть только свои строки данных.

## <a name="performance-impact-on-queries"></a>Влияние на запросы на производительность

Если в таблице включена политика RLS, то в запросах, обращающихся к этой таблице, возникнет некоторое влияние на производительность. Доступ к таблице на самом деле будет заменен запросом RLS, определенным для этой таблицы. Влияние запроса RLS на производительность, как правило, состоит из двух частей:

* Проверки членства в Azure Active Directory
* Фильтры, применяемые к данным

Пример:

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

Если пользователь не является частью *some_group@domain.com* , `IsRestrictedUser` будет вычислено значение `false` . Запрос, который будет оцениваться, аналогичен следующему:

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

Аналогично, если `IsRestrictedUser` значение равно `true` , `PartialData` будет оцениваться только запрос для.

### <a name="improve-query-performance-when-rls-is-used"></a>Повышение производительности запросов при использовании RLS

* Если фильтр применяется к столбцу с высоким количеством элементов, например DeviceID, рассмотрите возможность использования [политики секционирования](./partitioningpolicy.md) или [политики порядка строк](./roworderpolicy.md) .
* Если фильтр применяется к столбцу с низким средним количеством элементов, рекомендуется использовать [политику порядка строк](./roworderpolicy.md) .

## <a name="performance-impact-on-ingestion"></a>Влияние на производительность при приеме

На прием не влияет производительность.
