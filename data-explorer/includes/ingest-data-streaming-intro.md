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
ms.openlocfilehash: e6704592a5b0f1a984ea5651eb8073ac105fda3d
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423100"
---
Используйте прием потоковой передачи для загрузки данных, если требуется низкая задержка между приемом и запросом. Операция приема потоковой передачи завершается в течение 10 секунд, и данные сразу становятся доступными для запроса после завершения. Этот метод приема подходит для приема большого объема данных, например тысяч записей в секунду, распределенных по тысячам таблиц. Каждая таблица получает относительно небольшой объем данных, например несколько записей в секунду.

Используйте массовый прием вместо приема потоковой передачи, когда объем получаемых данных превышает 4 ГБ в час на таблицу.

Дополнительные сведения о различных методах приема см. в разделе [Обзор приема данных](../ingest-data-overview.md).
