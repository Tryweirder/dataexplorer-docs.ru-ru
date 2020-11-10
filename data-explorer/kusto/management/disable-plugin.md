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
ms.openlocfilehash: 1dd2649d4746506f0ef2c08d4615260babd594e1
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422111"
---
# <a name="disable-plugin"></a>. отключить подключаемый модуль

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

* [. Отображение подключаемых модулей](show-plugins.md)
* [. Включение подключаемого модуля](enable-plugin.md)

