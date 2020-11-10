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
ms.openlocfilehash: a44ebec6774374f4d38dfda3babe42f2f5e07ac6
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422106"
---
# <a name="enable-plugin"></a>. Включение подключаемого модуля

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

* [. отключить подключаемый модуль](disable-plugin.md)
* [. Отображение подключаемых модулей](show-plugins.md)

