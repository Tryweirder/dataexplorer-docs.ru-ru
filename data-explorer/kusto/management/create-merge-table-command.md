---
title: . Создание и слияние таблиц — Azure обозреватель данных
description: В этой статье описывается создание и слияние таблиц в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: afe5011717fd77d654eaf6c2b70e9ffbdea87128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967635"
---
# <a name="create-merge-table"></a>. CREATE — merge-таблица

Создание новой таблицы или расширение существующей. 

Команда должна выполняться в контексте определенной базы данных. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**

`.create-merge``table` *TableName* ([ColumnName: columnType],...)  [ `with` `(` [ `docstring` `=` *Документация*] [ `,` `folder` `=` *имя_папки*] `)` ]

Если таблица не существует, функция работает точно так же, как и команда ". Create Table".

Если таблица T существует и вы отправляете команду «. CREATE-merge Table T ( <columns specification> )», то:

* Любой столбец в <columns specification> , который ранее не существовал в t, будет добавлен в конец схемы t.
* Любой столбец в T, не являющийся, не будет <columns specification> удален из t.
* Любой столбец в <columns specification> , который существует в T, но с другим типом данных приведет к сбою команды.

**См. также:**

* [.create-merge tables](create-merge-tables-command.md)
* [.create table](create-table-command.md)
