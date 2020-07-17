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
ms.openlocfilehash: a748332c85839bac8466532ba3959810a6387834
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423071"
---
## <a name="disable-streaming-ingestion-on-your-cluster"></a>Отключение приема потоковой передачи в кластере

> [!WARNING]
> Отключение приема потоковой передачи может занять несколько часов.

Перед отключением приема потоковой передачи в кластере Azure обозреватель данных удалите [политику приема потоковой передачи](../kusto/management/streamingingestionpolicy.md) из всех соответствующих таблиц и баз данных. Удаление политики приема потоковой передачи активирует перестановку данных в кластере Azure обозреватель данных. Данные приема потоковой передачи перемещаются из первоначального хранилища в постоянное хранилище в хранилище столбцов (экстентов или сегментов). Этот процесс может занять от нескольких секунд до нескольких часов в зависимости от объема данных в первоначальном хранилище.
