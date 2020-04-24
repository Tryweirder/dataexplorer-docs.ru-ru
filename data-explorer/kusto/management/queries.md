---
title: Управление запросами - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление запросами в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: b888aa004b4c8b02cd4e1d130bb0b5319af018b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520525"
---
# <a name="queries-management"></a>Управление запросами

## <a name="show-queries"></a>запросы .show

Команда `.show` `queries` возвращает список запросов, достигающих конечного состояния, и что пользователь, ссылающийся на команду, имеет доступ к п.п.:


* [Админ базы данных или монитор базы данных](../management/access-control/role-based-authorization.md) могут видеть любую команду, которая была вызвана в их базе данных.
* Другие пользователи могут видеть только запросы, на которые они ссылались.

**Синтаксис**

`.show` `queries`

## <a name="show-running-queries"></a>.show запущенные запросы

`.show` `running` Команда `queries` возвращает список запросов, исполняемых в настоящее время пользователем, другим пользователем или всеми пользователями.

**Синтаксис**

```kusto
.show running queries
```

* (1) возвращает запросы, исполняемые в настоящее время, пользователем, ссылающимся на него (требует доступа к прочитанному).

## <a name="cancel-query"></a>запрос .cancel

Команда `.cancel` `query` запускает наилучшую попытку отменить определенный запрос, ранее запущенный тем же пользователем.

* Кластерные админы могут отменить любой запущенный запрос.
* Администрирование базы данных может отменить любой запущенный запрос, который был вызван в базе данных, в которой они имеют доступ к администрированию.
* Другие пользователи могут отменять только запущенные ими запросы. 

**Синтаксис**

`.cancel``query` *ClientRequestId*

* *ClientRequestId* является значение оригинальных запросов ClientRequestId `string` поле, как буквальное.

**Пример**

```kusto
.cancel query "KE.RunQuery;8f70e9ab-958f-4955-99df-d2a288b32b2c"
```

