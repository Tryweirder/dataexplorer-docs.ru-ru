---
title: Подключаемые модули для команды plugins в Azure обозреватель данных
description: В этой статье описываются команды управления подключаемыми модулями в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/02/2020
ms.openlocfilehash: 1db761d8c290c7aaea452cd0cb3d85f2f648221c
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422122"
---
# <a name="show-plugins"></a>. Отображение подключаемых модулей


Список всех подключаемых модулей кластера.

## <a name="syntax"></a>Синтаксис

`.show` `plugins`

## <a name="output"></a>Выходные данные

Возвращает таблицу, содержащую следующие поля:
* **PluginName** : имя подключаемого модуля.
* Enabled **(включено)** — логическое значение, указывающее, включен ли подключаемый модуль.

## <a name="example"></a>Пример

<!-- csl -->
```kusto
.show plugins
``` 

| PluginName | IsEnabled |
|---|---|
| autocluster | false |
| basket      | true  |

## <a name="next-steps"></a>Дальнейшие действия

* [. отключить подключаемый модуль](disable-plugin.md)
* [. Включение подключаемого модуля](enable-plugin.md)
