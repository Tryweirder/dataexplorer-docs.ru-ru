---
title: Включить имя файла
description: включить файл
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 1ee658d2c16bcf4174bb82d61ddc8c9b2d7d126f
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423224"
---
## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Использование приема потоковой передачи для приема данных в кластере

Поддерживаются два типа приема потоковой передачи:

* [**Концентратор событий**](../ingest-data-event-hub.md) или центр [**Интернета вещей**](../ingest-data-iot-hub.md), который используется в качестве источника данных.
* Для **пользовательского приема** требуется написать приложение, использующее одну из [клиентских библиотек](../kusto/api/client-libraries.md)Azure обозреватель данных. Пример приложения см. в разделе [Пример приема потоковой передачи](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) .

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Выбор соответствующего типа приема потоковой передачи

|Критерий|Концентратор событий|Пользовательское получение|
|---------|---------|---------|
|Задержка данных между инициацией приема и данными, доступными для запроса | Более длительная задержка | Короткая задержка  |
|Затраты на разработку | Быстрая и простая настройка, без дополнительных затрат на разработку | Высокая нагрузка на разработку приложений для управления ошибками и обеспечения согласованности данных |
