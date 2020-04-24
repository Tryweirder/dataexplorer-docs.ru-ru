---
title: Безопасность уровня строки (Предварительный просмотр) - Проводник данных Azure (ru) Документы Майкрософт
description: В этой статье описывается безопасность уровня строки (Preview) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: 548b0e9e40cfd709b40e548fe7e0a8ad42aa4abc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520287"
---
# <a name="row-level-security-preview"></a>Безопасность уровня строки (Предварительный просмотр)

Используйте контекст членства в группе или выполнения для управления доступом к строкам в таблице базы данных.

Security Row Level (RLS) упрощает проектирование и кодирование безопасности в приложении, позволяя применять ограничения на доступ к строке данных. Например, ограничить доступ пользователей к строкам, относящимся к их отделу, или ограничить доступ клиентов только к данным, относящимся к их компании.

Логика ограничения доступа находится в уровне базы данных, а не в сторону от данных в другом уровне приложения. Система базы данных применяет ограничения доступа каждый раз, когда попытка доступа к данным с любого уровня. Это делает систему безопасности более надежной и устойчивой за счет уменьшения контактной зоны системы безопасности.

RLS позволяет предоставлять доступ к другим приложениям и/или пользователям только к определенной части таблицы. Например, может понадобиться:

* Предоставлять доступ только к строкам, отвечающим некоторым критериям
* Анонимизация данных в некоторых столбцах
* Оба вышеуказанных

> [!NOTE]
> Политика безопасности уровня строки не может быть включена на столе:
> * Для [которого настроен непрерывный экспорт данных.](../management/data-export/continuous-data-export.md)
> * На это ссылается запрос некоторых [обновлений политики](./updatepolicy.md).
> * На котором [настроена политика ограниченного доступа к представлениям.](./restrictedviewaccesspolicy.md)

Подробнее о командах управления для управления политикой безопасности уровня [строки можно узнать здесь.](../management/row-level-security-policy.md)

## <a name="examples"></a>Примеры

В таблице `Sales`с именем , каждая строка содержит сведения о продаже. В одной из столбцов содержится имя продавца.
Вместо того, чтобы предоставить продавцам доступ ко всем записям в, `Sales`вы можете включить политику безопасности уровня строки на этом столе, чтобы вернуть только записи, где продавец является текущим пользователем:

```kusto
Sales | where SalesPersonAadUser == current_principal()
```

Вы также можете замаскировать номер кредитной карты:

```kusto
Sales | where SalesPersonAadUser == current_principal() | extend CreditCardNumber = "****"
```

Если вы хотите, чтобы каждый продавец видел все продажи в конкретной стране, вы можете определить запрос, аналогичный следующему:

```kusto
let UserToCountryMapping = datatable(User:string, Country:string)
[
  "john@domain.com", "USA",
  "anna@domain.com", "France"
];
Sales
| where Country in (UserToCountryMapping | where User == current_principal_details()["UserPrincipalName"] | project Country)
```

Если у вас есть группа AAD, содержащая менеджеров продавцов, вы можете захотеть, чтобы они имели доступ ко всем строкам. Это может быть достигнуто с помощью следующего запроса в политике безопасности уровня строки:

```kusto
let IsManager = current_principal_is_member_of('aadgroup=sales_managers@domain.com');
let AllData = Sales | where IsManager;
let PartialData = Sales | where not(IsManager) and (SalesPersonAadUser == current_principal());
union AllData, PartialData
| extend CreditCardNumber = "****"
```

Как правило, если у вас есть несколько групп AAD, и вы хотите, чтобы члены каждой группы видели различные подмножества данных, вы можете следовать этой структуре для запроса RLS (при условии, что пользователь может принадлежать только к одной группе AAD):

```kusto
let IsInGroup1 = current_principal_is_member_of('aadgroup=group1@domain.com');
let IsInGroup2 = current_principal_is_member_of('aadgroup=group2@domain.com');
let IsInGroup3 = current_principal_is_member_of('aadgroup=group3@domain.com');
let DataForGroup1 = Customers | where IsInGroup1 and <filtering specific for group1>;
let DataForGroup2 = Customers | where IsInGroup2 and <filtering specific for group2>;
let DataForGroup3 = Customers | where IsInGroup3 and <filtering specific for group3>;
union DataForGroup1, DataForGroup2, DataForGroup3
```

## <a name="more-use-cases"></a>Больше случаев использования

* Сотрудник службы поддержки колл-центра может идентифицировать абонентов по нескольким цифрам их номера социального страхования или номера кредитной карты. Эти цифры не должны быть полностью подвержены поддержке лица. Политика RLS может быть применена на столе, чтобы замаскировать все, кроме последних четырех цифр любого социального обеспечения или номера кредитной карты в наборе результатов любого запроса.
* Установите политику RLS, которая маскирует личную идентифицируемую информацию (PII), позволяя разработчикам заготавлив производственные среды для устранения неполадок без нарушения правил соответствия.
* Больница может установить политику RLS, которая позволяет медсестрам просматривать строки данных только для своих пациентов.
* Банк может установить политику RLS для ограничения доступа к финансовым строкам данных на основе бизнес-подразделения или роли сотрудника.
* Мультитенантное приложение может хранить данные от многих арендаторов в едином наборе таблиц (что очень эффективно). Они будут использовать политику RLS для обеспечения логического разделения строк данных каждого клиента от строк каждого другого арендатора, так что каждый арендатор может видеть только свои строки данных.

## <a name="performance-impact-on-queries"></a>Влияние производительности на запросы

Когда политика RLS включена в таблице, это окажет определенное влияние на производительность запросов, которые получают доступ к этой таблице. Доступ к таблице фактически будет заменен запросом RLS, который определен на этой таблице. Влияние запроса на производительность запроса RLS обычно состоит из двух частей:

* Проверки членства в Active Directory Azure
* Фильтры, применяемые на данных

Пример:

```kusto
let IsRestrictedUser = current_principal_is_member_of('aadgroup=some_group@domain.com');
let AllData = MyTable | where not(IsRestrictedUser);
let PartialData = MyTable | where IsRestrictedUser and (...);
union AllData, PartialData
```

Если пользователь не является some_group@domain.comчастью, `IsRestrictedUser` то `false`будет оцениваться, так что запрос, который будет оцениваться будет похож на этот:

```kusto
let AllData = MyTable;           // the condition evaluates to `true`, so the filter is dropped
let PartialData = <empty table>; // the condition evaluates to `false`, so the whole expression is replaced with an empty table
union AllData, PartialData       // this will just return AllData, as PartialData is empty
```

Аналогичным образом, `IsRestrictedUser` если `true`оценивается, то `PartialData` будет оцениваться только запрос.

## <a name="performance-impact-on-ingestion"></a>Влияние производительности на проглатку

Отсутствует.