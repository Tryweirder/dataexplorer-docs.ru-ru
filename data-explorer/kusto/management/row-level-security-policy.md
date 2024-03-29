---
title: Политика Ровлевелсекурити в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается политика Ровлевелсекурити в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/11/2020
ms.openlocfilehash: 25ad7040b0318206a712a9a7fb8d3be58e0f47f3
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148445"
---
# <a name="row_level_security-policy-command"></a>Команда политики row_level_security

В этой статье описываются команды, используемые для настройки [политики row_level_security](rowlevelsecuritypolicy.md) для таблиц базы данных.

## <a name="displaying-the-policy"></a>Отображение политики

Чтобы отобразить политику, используйте следующую команду:

```kusto
.show table <table_name> policy row_level_security
```

## <a name="configuring-the-policy"></a>Настройка политики

Включение политики row_level_security для таблицы:

```kusto
.alter table <table_name> policy row_level_security enable "<query>"
```

Отключение политики row_level_security для таблицы:

```kusto
.alter table <table_name> policy row_level_security disable "<query>"
```

Даже если политика отключена, можно принудительно применить ее к одному запросу. Перед запросом введите следующую строку:

`set query_force_row_level_security;`

Это полезно, если требуется использовать различные запросы для row_level_security, но не нужно, чтобы они действовали на пользователей. Убедившись в запросе, включите политику.

> [!NOTE]
> К следующим ограничениям относятся `query` :
>
> * Запрос должен создавать точно такую же схему, как и таблица, в которой определена политика. То есть результат запроса должен возвращать те же столбцы, что и исходная таблица в том же порядке, что и те же имена и типы.
> * В запросе могут использоваться только следующие операторы: `extend` , `where` , `project` , `project-away` , `project-keep` , `project-rename` , `project-reorder` `join` и `union` .
> * Запрос не может ссылаться на другие таблицы, для которых включена RLS.
> * Запрос может быть любым из следующих элементов или их сочетанием:
>    * Запрос (например, `<table_name> | extend CreditCardNumber = "****"` )
>    * Функция (например, `AnonymizeSensitiveData` )
>    * DataTable (например, `datatable(Col1:datetime, Col2:string) [...]` )

> [!TIP]
> Эти функции часто используются для row_level_securityных запросов:
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

**Примечание о производительности** . `UserCanSeeFullNumbers` вычисляется сначала, а затем либо `AllData` `PartialData` вычисляется, либо будет оцениваться, но не оба сразу, что является ожидаемым результатом.
Дополнительные сведения о влиянии на производительность RLS можно получить [здесь](rowlevelsecuritypolicy.md#performance-impact-on-queries).

## <a name="deleting-the-policy"></a>Удаление политики

```kusto
.delete table <table_name> policy row_level_security
```
