---
title: . изменение тегов экстента в Azure обозреватель данных
description: В этой статье описывается команда ALTER экстент в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 61dba69c3ec40ec13960e9ddf266e47fe88ef3c6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321749"
---
# <a name="alter-extent-tags"></a>. изменение тегов экстента

Команда выполняется в контексте определенной базы данных. Он изменяет заданные [теги экстентов](extents-overview.md#extent-tagging) всех экстентов, возвращенных запросом.

Экстенты и теги для изменения указываются с помощью запроса Kusto, возвращающего набор записей со столбцом «Екстентид».

Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) для всех связанных таблиц.

> [!NOTE]
> Сегменты данных называются **экстентами** в Kusto, а все команды используют "экстент" или "экстенты" в качестве синонима.
> Дополнительные сведения о экстентах см. в статье [Общие сведения о экстентах (сегментах данных)](extents-overview.md).

## <a name="syntax"></a>Синтаксис

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *Тагн*"] `)`  <|  *запрос*

`async` (необязательно): асинхронное выполнение команды.
   * Возвращается идентификатор операции (GUID). 
   * Состояние операции можно отслеживать. Используйте [`.show operations`](operations.md#show-operations) команду.
   * Вы можете получить результаты успешного выполнения. Используйте [`.show operation details`](operations.md#show-operation-details) команду.

## <a name="restrictions"></a>Ограничения

Все экстенты должны находиться в базе данных контекста и должны принадлежать к одной и той же таблице.

## <a name="return-output"></a>Возврат выходных данных

|Выходной параметр |Тип |Описание|
|---|---|---|
|оригиналекстентид |строка |Уникальный идентификатор (GUID) для исходного экстента, теги которого были изменены. Экстент удаляется как часть операции.|
|ресултекстентид |строка |Уникальный идентификатор (GUID) экстента результатов, в котором были изменены Теги. Экстент создается и добавляется как часть операции. При сбое — "Failed".|
|ресултекстенттагс |строка |Коллекция тегов, на которые помечается область результатов, или значение null, если операция завершается ошибкой.|
|Сведения |строка |Включает сведения об ошибке в случае сбоя операции.|

## <a name="examples"></a>Примеры

### <a name="alter-tags"></a>Изменение тегов 

Изменение тегов всех экстентов в таблице `MyTable` на `MyTag`

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

### <a name="alter-tags-of-all-extents"></a>Изменение тегов всех экстентов

Изменять теги всех экстентов в таблице `MyTable` , с тегами `drop-by:MyTag` на `drop-by:MyNewTag` и `MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

## <a name="sample-output"></a>Пример выходных данных

|оригиналекстентид |ресултекстентид | ресултекстенттагс | Сведения
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Drop-By: Миневтаг Мйосерневтаг| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | Drop-By: Миневтаг Мйосерневтаг| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f | Drop-By: Миневтаг Мйосерневтаг| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | Drop-By: Миневтаг Мйосерневтаг| 
