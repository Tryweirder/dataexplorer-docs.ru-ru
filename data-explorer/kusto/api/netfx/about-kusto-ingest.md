---
title: Клиентская библиотека приема Kusto в Azure обозреватель данных
description: В этой статье описывается Kustoная клиентская библиотека в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/18/2020
ms.openlocfilehash: 33d3515a8465a1e9c3397e675a51c95aa3f00d42
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373839"
---
# <a name="kusto-ingest-client-library"></a>Клиентская библиотека приема Kusto 

`Kusto.Ingest` Библиотека — это библиотека .NET 4.6.2 для отправки данных в службу Kusto.
Он принимает зависимости от следующих библиотек и пакетов SDK:

* ADAL для проверки подлинности Azure AD
* Клиент службы хранилища Azure

Методы приема определяются интерфейсом [икустоинжестклиент](kusto-ingest-client-reference.md#interface-ikustoingestclient) .  Методы обрабатывают прием данных из потоков, IDataReader, локальных файлов и больших двоичных объектов Azure в синхронных и асинхронных режимах.

## <a name="ingest-client-flavors"></a>Флаги приема клиента

Существует две основные разновидности принимающего клиента: в очереди и прямо.

### <a name="queued-ingestion"></a>Прием с постановкой в очередь

Режим приема в очереди, определенный параметром [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), ограничивает зависимость клиентского кода от службы Kusto. Прием выполняется путем отправки сообщения приема Kusto в очередь Azure, которая затем получается из службы Управление данными Kusto (приема). Все промежуточные элементы хранилища будут созданы принимающим клиентом с помощью ресурсов из службы Управление данными Kusto.

**Ниже перечислены преимущества режима очереди.**

* Отделяет процесс приема данных от службы ядра Kusto
* Позволяет сохранять запросы на прием, если служба Kusto Engine (или приема) недоступна
* Повышение производительности за счет эффективного и управляемого агрегирования входящих данных службой приема 
* Позволяет службе приема Kusto управлять нагрузкой приема в службе подсистемы Kusto
* Повторяет попытку службы приема Kusto, если это необходимо, для временных сбоев приема, например для регулирования XStore
* Предоставляет удобный механизм отслеживания хода выполнения и результатов каждого запроса приема.

На следующей схеме показано взаимодействие клиента приема в очереди с Kusto.

:::image type="content" source="../images/about-kusto-ingest/queued-ingest.png" alt-text="Диаграмма, показывающая, как библиотека Kusto. приема отправляет запросы в службу Kusto в запрашиваемом режиме приема.":::
 
### <a name="direct-ingestion"></a>Прямой прием

Режим прямого приема, определенный параметром Икустодиректинжестклиент, заставляет прямое взаимодействие со службой подсистемы Kusto. В этом режиме служба приема Kusto не обладает средним уровнем управления данными и не управляет ими. Каждый запрос приема в конечном итоге преобразуется в `.ingest` команду, которая выполняется непосредственно в службе Kusto Engine.

На следующей схеме показано взаимодействие клиента прямого приема с Kusto.

:::image type="content" source="../images/about-kusto-ingest/direct-ingest.png" alt-text="Диаграмма, показывающая, как библиотека Kusto. приема отправляет запросы в службу Kusto в режиме прямого приема.":::

> [!NOTE]
> Прямой режим не рекомендуется для решений приема производственного уровня.

**К преимуществам прямого режима относятся:**

* Низкая задержка без агрегирования. Однако низкая задержка также может быть достигнута посредством очередного приема
* При использовании синхронных методов завершение метода указывает на окончание операции приема.

**Недостатки прямого режима:**

* Клиентский код должен реализовать любую логику обработки повторов или ошибок
* Приемы невозможны, если служба ядра Kusto недоступна
* Клиентский код может перегрузить службу Kusto Engine с запросами приема, так как она не учитывает емкость службы ядра.

## <a name="ingestion-best-practices"></a>Рекомендации по приему

Рекомендации по [приему](kusto-ingest-best-practices.md) предоставляют cogs (стоимость проданных товаров) и пропускную способность для приема.

* **Безопасность потоков —** Реализации клиентов, принимающие Kusto, являются потокобезопасными и предназначены для повторного использования. Нет необходимости создавать экземпляр `KustoQueuedIngestClient` класса для каждой или нескольких операций приема. Для `KustoQueuedIngestClient` каждого пользовательского процесса в целевом кластере Kusto требуется один экземпляр. Выполнение нескольких экземпляров является продуктивным и может вызвать DoS в кластере Управление данными.

* **Поддерживаемые форматы данных —** Если вы используете прием машинного кода, если это еще не сделано, отправьте данные в один или несколько больших двоичных объектов службы хранилища Azure. В настоящее время Поддерживаемые форматы больших двоичных объектов описаны в разделе [Поддерживаемые форматы данных](../../../ingestion-supported-formats.md).

* **Сопоставление схем —** 
 [Сопоставления схем](../../management/mappings.md) помогают с детерминированной привязкой полей исходных данных к столбцам целевой таблицы.

* **Разрешения на прием-** 
 [Разрешения](kusto-ingest-client-permissions.md) на прием Kusto объясняют настройки разрешений, необходимые для успешного приема с помощью `Kusto.Ingest` пакета.

* **Использование —** Как было сказано ранее, для устойчивых и крупномасштабных решений приема для Kusto рекомендуется использовать **кустокуеуединжестклиент**.
Чтобы уменьшить ненужную нагрузку на службу Kusto, мы рекомендуем использовать один экземпляр Kusto принимающего клиента (в очереди или прямой) для каждого процесса в кластере Kusto. Реализация клиента, принимающего Kusto, является потокобезопасной и полностью передается.

## <a name="next-steps"></a>Дальнейшие шаги

* [Kusto. Принимающая ссылка на клиент](kusto-ingest-client-reference.md) содержит полную ссылку на Kusto приема клиентских интерфейсов и реализаций. Вы найдете сведения о том, как создавать клиенты приема, расширять запросы на прием, управлять ходом приема и многое другое.

* [Kusto. принимающее состояние операции](kusto-ingest-client-status.md) описывает функции кустокуеуединжестклиент для отслеживания состояния приема

* [Kusto. прием ошибок](kusto-ingest-client-errors.md) содержит описание Kusto, попринимающих ошибки и исключения клиента

* В [примерах Kusto. приема](kusto-ingest-client-examples.md) показаны фрагменты кода, демонстрирующие различные методы приема данных в Kusto

* Прием [данных без Kusto. Принимающая библиотека](kusto-ingest-client-rest.md) объясняет, как реализовать постановку в очередь приема Kusto с помощью API-интерфейсов Kusto и без зависеть от `Kusto.Ingest` библиотеки.

