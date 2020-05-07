---
title: Политика заказа строки - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описана политика заказа строк в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4dca1a4083a6141eb94d9bf3cf69f7134ebfca04
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520219"
---
# <a name="row-order-policy"></a>Политика порядка строк

Политика заказа строки является дополнительной политикой, установленной на столах, которая обеспечивает «подсказку» Кусто при желаемом заказе строк в осколок данных.

Основная цель политики заключается не в улучшении сжатия (хотя это потенциальный побочный эффект), а в повышении производительности запросов, которые, как известно, сужаются до небольшого поднабора значений в заказанных столбцах.

Применение политики является целесообразным, когда:
* Большинство запросов фильтруют определенные значения определенного столбца большого размера (например, "идентификатор приложения" или "идентификатор арендатора")
* Данные, попаденные в таблицу, вряд ли будут предварительно заказаны в соответствии с этой колонкой.

Хотя нет жестких ограничений, установленных на количество столбцов (сортировать ключи), которые могут быть определены как часть политики, каждый дополнительный столбец добавляет некоторые накладные расходы в процессе приема, и по мере добавления большего числа столбцов - эффективное возвращение уменьшается.

> [!NOTE]
> Как только политика будет применена к таблице, она повлияет на данные, посягаемые с этого момента.

Команды управления для управления политиками заказа строки можно найти [здесь](../management/roworder-policy.md)