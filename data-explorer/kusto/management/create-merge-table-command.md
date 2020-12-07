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
ms.openlocfilehash: 554f6ed623b5a3be12a360fab0b1d5aa6eb4c084
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320967"
---
# <a name="create-merge-table"></a>.create-merge table

Создание новой таблицы или расширение существующей. 

Команда должна выполняться в контексте определенной базы данных. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

## <a name="syntax"></a>Синтаксис

`.create-merge``table` *TableName* ([ColumnName: columnType],...)  [ `with` `(` [ `docstring` `=` *Документация*] [ `,` `folder` `=` *имя_папки*] `)` ]

Если таблица не существует, функция работает точно так же, как и `.create table` команда.

Если таблица T существует и вы отправляете `.create-merge table T (<columns specification>)` команду, то:

* Любой столбец в <columns specification> , который ранее не существовал в t, будет добавлен в конец схемы t.
* Любой столбец в T, не являющийся, не будет <columns specification> удален из t.
* Любой столбец в <columns specification> , который существует в T, но с другим типом данных приведет к сбою команды.

## <a name="see-also"></a>См. также раздел

* [`.create-merge tables`](create-merge-tables-command.md)
* [`.create table`](create-table-command.md)
