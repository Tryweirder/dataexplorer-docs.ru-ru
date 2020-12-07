---
title: Сведения о системе — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются сведения о системе в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/07/2019
ms.openlocfilehash: 22ad4956d94f445b26e49e1e73ed54e86568d1ad
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321222"
---
# <a name="system-information"></a>Сведения о системе

В этом разделе перечислены команды, доступные `Database Admins` и `Database Monitors` роли для изучения использования, наблюдения за операциями и исследования сбоев приема.

* [`.show queries`](queries.md) — Отображает сведения о завершенных и выполняющихся запросах.
* [`.show commands`](commands.md) — Отображает сведения о завершенных командах и использовании ресурсов.
* [`.show commands-and-queries`](commands-and-queries.md) — Отображает сведения о завершенных командах и запросах, а также об использовании ресурсов.
* [`.show journal`](journal.md) — Отображает журнал операций с метаданными.
* [`.show operations`](operations.md) — Отображает операции администрирования, выполняемые и завершенные с момента последнего выборе узла администратора.
* [`.show failed ingestions`](ingestionfailures.md) — Отображает сведения об ошибках, возникших во время приема данных в кластере.