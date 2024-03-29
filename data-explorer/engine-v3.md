---
title: Azure обозреватель данных Kusto EngineV3 (Предварительная версия)
description: Дополнительные сведения об Azure обозреватель данных (Kusto) EngineV3
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 133f01498d4cf430d7fdc2649df88186610b3954
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388976"
---
# <a name="enginev3---preview"></a>EngineV3 — Предварительная версия

Kusto EngineV3 — это хранилище следующего поколения Azure обозреватель данных и обработчик запросов. Он предназначен для обеспечения непревзойденной производительности при приеме и запросе данных телеметрии, журналов и временных рядов.

EngineV3 включает новый оптимизированный формат хранения и индексы. EngineV3 использует расширенные оптимизации запросов статистики данных для создания оптимального плана запроса и выполнения JIT-компиляции запроса. В EngineV3 также улучшен кэш диска, что приводит к повышению производительности запросов с последовательностью улучшения по сравнению с текущим подсистемой (EngineV2). EngineV3 помещает основу для следующей волны нововведений службы обозреватель данных Azure.

Кластер Azure обозреватель данных, работающий в режиме EngineV3, полностью совместим с EngineV2, поэтому перенос данных не требуется.

> [!IMPORTANT]
> EngineV3 будет доступен на следующих этапах:
>
> 1. Общедоступная Предварительная версия (текущее состояние): пользователи могут создавать новые кластеры в режиме EngineV3. В течение периода общедоступной предварительной версии кластеры не подставляются по соглашению об уровне обслуживания и не платите за разметку Azure обозреватель данных. Стоимость инфраструктуры взимается как обычно.
> 1. Общая доступность (общедоступная версия). по умолчанию все новые кластеры создаются в режиме EngineV3. Соглашение об уровне обслуживания применяется ко всем рабочим кластерам EngineV3 и EngineV2.
> 1. POST общедоступной версии: существующие рабочие нагрузки, выполняемые в EngineV2, переносятся в EngineV3. Плата за разметку обозреватель данных Azure будет возобновлена.

## <a name="how-enginev3-works"></a>Как работает EngineV3

EngineV3 — это дополнительная подсистема хранилища столбцов, выполняющаяся параллельно с существующим хранилищем столбцов (EngineV2) и хранилищем строк (используется для приема потоковой передачи). Таблицы могут включать данные из всех трех магазинов одновременно, и это "Федерация" данных прозрачна с точки зрения пользователя.

:::image type="content" source="media\engine-v3\engine-v3-architecture.png" alt-text="Схематическое представление архитектуры Azure обозреватель данных/Kusto EngineV3":::

Все данные, принимаемые в таблицы, разделены на сегменты, которые являются горизонтальными срезами таблицы. Каждый сегмент обычно содержит несколько миллионов записей и кодируется и индексируется независимо от других сегментов. Эта функция позволяет подсистеме достичь линейного масштабирования пропускной способности приема.

Сегменты равномерно распределяются между узлами кластера, где они кэшируются как на локальном SSD, так и в памяти. Планировщик запросов и обработчик запросов подготавливают и выполняют высокораспределенный и параллельный запрос, который дает преимущества при этом распределении сегментов и кэшировании.

EngineV3 специализируется на оптимизации этой «нижней части» распределенного запроса.

## <a name="performance"></a>Производительность

Улучшенная производительность и повышение скорости запросов поступают из двух основных изменений в подсистеме:

* **Новый и улучшенный формат хранилища сегментов.** Как и в случае с EngineV2, формат хранения представляет собой хранилище сжатых столбцов с особым вниманием на неструктурированные (текстовые) и частично структурированные типы данных. EngineV3 улучшает кодировку этих различных типов данных. Индексы были переработаны для повышения степени детализации, что позволяет вычислять части запроса, основанные на индексе, не проверяя данные.
* **Перепроектирование обработчика запросов сегментирования низкого уровня.** Новый запрос сегмента — это JIT-компиляция в высокоэффективный машинный код, что приводит к быстрой и эффективной логике оценки запросов с плавким предохранителем. Компиляция запроса осуществляется с помощью статистики данных, собранной из всех сегментов и адаптированных к конкретным кодировкам столбцов.

Влияние EngineV3 на производительность зависит от набора данных, шаблонов запросов, параллелизма и номеров SKU виртуальных машин. При тестировании производительности использовался набор данных 100 ТБ, и в нем были рассмотрены различные сценарии, охватывающие анализ структурированных, неструктурированных и частично структурированных данных. С одинаковым уровнем параллелизма и использованием одинаковой конфигурации оборудования повышение производительности было в среднем ~ 8X. Фактическое улучшение производительности зависит от запроса и набора данных.

## <a name="create-an-enginev3-cluster"></a>Создание кластера EngineV3

Чтобы [создать новый кластер](create-cluster-database-portal.md) с EngineV3, на вкладке " **основы** " на экране создания кластера установите флажок **использовать подсистему версии v3 Preview** :

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="Снимок экрана с флажком &quot;использовать предварительную версию подсистемы версии 3&quot; при создании кластера":::

## <a name="next-steps"></a>Дальнейшие действия

[Прием данных с помощью Azure обозреватель данных](ingest-data-overview.md)
