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
ms.openlocfilehash: 19dc7db9e344a516b5c92917dccbf8362b1ca858
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102880"
---
# <a name="create-merge-table"></a>.create-merge table

Создание новой таблицы или расширение существующей. 

Команда должна выполняться в контексте определенной базы данных. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

## <a name="syntax"></a>Синтаксис

`.create-merge``table` *TableName* ([ColumnName: columnType],...)  [ `with` `(` [ `docstring` `=` *Документация*] [ `,` `folder` `=` *имя_папки*] `)` ]

Если таблица не существует, функция работает точно так же, как и команда ". Create Table".

Если таблица T существует и вы отправляете команду «. CREATE-merge Table T ( <columns specification> )», то:

* Любой столбец в <columns specification> , который ранее не существовал в t, будет добавлен в конец схемы t.
* Любой столбец в T, не являющийся, не будет <columns specification> удален из t.
* Любой столбец в <columns specification> , который существует в T, но с другим типом данных приведет к сбою команды.

## <a name="see-also"></a>См. также раздел

* [.create-merge tables](create-merge-tables-command.md)
* [.create table](create-table-command.md)
