---
title: Политика RowLevelSecurity (Предварительный просмотр) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается политика RowLevelSecurity (Preview) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2020
ms.openlocfilehash: bf8ee8bc4c43c4ed3bcd320ce5b4778f33c3cc64
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520304"
---
# <a name="rowlevelsecurity-policy-preview"></a>Политика RowLevelSecurity (Предварительный просмотр)

В этой статье описаны команды, используемые для настройки [политики row_level_security](rowlevelsecuritypolicy.md) для таблиц баз данных.

## <a name="displaying-the-policy"></a>Отображение политики

Для отображения политики используйте следующую команду:

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>Настройка политики

Включить политику row_level_security на столе:

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

Отключить row_level_security политику на столе:

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

Даже если политика отключена, можно заставить ее применить сярприза к одному запросу. Введите следующую строку перед запросом:

`set query_force_row_level_security;`

Это полезно, если вы хотите попробовать различные запросы для row_level_security, но не хотите, чтобы он действительно вступил в силу на пользователей. Как только вы уверены в запросе, включите политику.

> [!NOTE]
> Следующие ограничения распространяются на: `query`
>
> * Запрос должен создавать точно такую же схему, как и таблица, по которой определяется политика. То есть результат запроса должен возвращать точно такие же столбцы, как исходная таблица, в том же порядке, с теми же именами и типами.
> * Запрос может использоваться только следующими `where` `project`операторами: `project-reorder` `extend` `union`, , `project-away` `project-rename`, и .
> * Запрос не может ссылаться на таблицы, кроме той, на которой определена политика.
> * Запрос может быть любым из следующих, или их сочетание:
>    * Запрос (например, `<table_name> | extend CreditCardNumber = "****"`)
>    * Функция (например, `AnonymizeSensitiveData`)
>    * Таблица данных `datatable(Col1:datetime, Col2:string) [...]`(например, )

> [!TIP]
> Эти функции часто полезны для row_level_security запросов:
> * [current_principal()](../query/current-principalfunction.md)
> * [current_principal_details()](../query/current-principal-detailsfunction.md)
> * [current_principal_is_member_of()](../query/current-principal-ismemberoffunction.md)

**Пример**

```kusto
.create-or-alter function with () TrimCreditCardNumbers() {
    let UserCanSeeFullNumbers = current_principal_is_member_of('aadgroup=super_group@domain.com');
    let AllData = Customers | where UserCanSeeFullNumbers;
    let PartialData = Customers | where not(UserCanSeeFullNumbers) | extend CreditCardNumber = "****";
    union AllData, PartialData
}

.alter table Customers policy row_level_security enable "TrimCreditCardNumbers"
```
**Примечание производительности:** `UserCanSeeFullNumbers` будет оцениваться `AllData` сначала, а затем либо или `PartialData` будет оцениваться, но не оба, что является ожидаемым результатом.
Вы можете прочитать больше о влиянии производительности RLS [здесь](rowlevelsecuritypolicy.md#performance-impact-on-queries).

## <a name="deleting-the-policy"></a>Удаляние политики

```kusto
.delete table <table_name> policy row_level_security
```