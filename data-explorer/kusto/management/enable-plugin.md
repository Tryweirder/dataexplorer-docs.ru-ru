---
title: Команда включения подключаемого модуля — Azure обозреватель данных
description: В этой статье описывается команда управления подключаемыми модулями. Включите подключаемый модуль в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: a4da3848fa459cf5fae8a7a73f8b1f318ce7e858
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321477"
---
# <a name="enable-plugin"></a>.enable plugin

Включает подключаемый модуль.

Для этой команды требуется `All Databases admin` разрешение.

## <a name="syntax"></a>Синтаксис

`.enable``plugin` *PluginName*

## <a name="example"></a>Пример

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>Дальнейшие действия

* [`.disable plugin`](disable-plugin.md)
* [`.show plugins`](show-plugins.md)

