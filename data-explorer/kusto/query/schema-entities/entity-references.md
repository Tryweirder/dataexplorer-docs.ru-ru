---
title: Ссылки на сущность - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны ссылки сущности в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abbf63de632ff2ff5fb721dddc256c5ee62d4966
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509407"
---
# <a name="entity-references"></a>Ссылки на сущности

Именованные сущности схемы Kusto (базы данных, таблицы, столбцы и сохраненные функции, но не кластеры) ссылаются в запросе, используя их имена. Если контейнер сущности является недвусмысленным в текущем контексте, имя сущности может быть использовано без дополнительной квалификации. Например, при запуске запроса `DB`в отношении подназвание `T` базы данных можно `T`ссылаться на таблицу, называемую в этой базе данных, просто используя свое имя.

Если, с другой стороны, контейнер сущности не доступен из контекста, или кто-то хочет ссылаться на сущность из контейнера, отличающегося от контейнера в контексте, необходимо использовать **квалифицированное имя**объекта, которое является конкатией имени объекта для контейнера (и, возможно, его контейнера и т.д.) Таким образом, запрос, `DB` запущенный в `T1` отношении базы `DB1` данных, может `database("DB1").T1`ссылаться на таблицу в другой базе данных одного и `cluster("https://C2.kusto.windows.net/").database("DB2").T2`того же кластера с помощью, и если он хочет ссылаться на таблицу из другого кластера, он может сделать это, например, с помощью .

Ссылки сущности могут также иметь имя сущности, если оно уникально в контексте контейнера сущности. Посмотреть [сущность красивые имена](./entity-names.md#entity-pretty-names).

## <a name="wildcard-matching-for-entity-names"></a>Подбор уайлдкартов для имен сущностей

В некоторых контекстах можно использовать`*`подстановочный знак (), чтобы соответствовать всем или части имени сущности. Например, в следующем запросе отсылаются все таблицы текущей базы данных, а также все таблицы в базе данных, `DB` имя которых начинается с: `T`

```kusto
union *, database("DB1").T*
```

Примечание: Соответствие Wildcard не соответствует именам сущностей, которые начинаются с знака доллара ().`$`
Такие названия зарезервированы системой.


