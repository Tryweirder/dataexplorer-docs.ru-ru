---
title: Отключение команд подключаемого модуля — Azure обозреватель данных
description: В этой статье описывается команда управления подключаемыми модулями. Отключите подключаемый модуль в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1063355f880f26a321eb6416180ae9764575f0be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320899"
---
# <a name="disable-plugin"></a>.disable plugin

Отключает подключаемый модуль.

Для этой команды требуется `All Databases admin` разрешение.

## <a name="syntax"></a>Синтаксис

`.disable``plugin` *PluginName*

## <a name="example"></a>Пример
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>Дальнейшие действия

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)

