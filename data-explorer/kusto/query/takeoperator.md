---
title: оператор Take — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор Take в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 569554379ffe672ed75fd15da127f03fea35f6d1
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748388"
---
# <a name="take-operator"></a>Оператор take

Возврат к указанному числу строк.

```kusto
T | take 5
```

Нет гарантии, какие записи возвращаются, если исходные данные не сортируются.

> [!NOTE]
> `take` — простой, быстрый и эффективный способ просмотра небольшого примера записей при интерактивном просмотре данных, но имейте в виду, что он не гарантирует согласованность результатов при многократном выполнении, даже если набор данных не изменился.
> Даже если число строк, возвращаемых запросом, не ограничено запросом (оператор не `take` используется), Kusto ограничивает это число по умолчанию. Дополнительные сведения см. в разделе [Kusto Query Limits](../concepts/querylimits.md).

## <a name="syntax"></a>Синтаксис

`take`*NumberOfRows* 
 NumberOfRows `limit` *NumberOfRows*

( `take` и `limit` являются синонимами.)

## <a name="paging-of-query-results"></a>Разбиение результатов запроса на страницы

Ниже приведены методы реализации разбиения на страницы.

* Экспорт результата запроса во внешнее хранилище и разбиение на страницы созданных данных.
* Напишите приложение среднего уровня, которое предоставляет API подкачки с отслеживанием состояния путем кэширования результатов запроса Kusto.
* Используйте разбивку на страницы в [результатах сохраненного запроса](../management/stored-query-results.md#pagination) .


## <a name="see-also"></a>См. также раздел

* [Оператор sort](sortoperator.md)
* [Оператор top](topoperator.md)
* [Вложенный оператор верхнего уровня](topnestedoperator.md)
