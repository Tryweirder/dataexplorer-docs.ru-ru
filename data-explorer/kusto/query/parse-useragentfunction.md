---
title: parse_user_agent () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается parse_user_agent () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ebe85c622dda116366e30ba22e2e495e4cb76ac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511736"
---
# <a name="parse_user_agent"></a>parse_user_agent()

Интерпретирует строку пользователя-агента, которая идентифицирует браузер пользователя и предоставляет определенные системные данные серверам, на которых размещается веб-сайты, которые посещает пользователь. Результат возвращается [`dynamic`](./scalar-data-types/dynamic.md)как . 

**Синтаксис**

`parse_user_agent(`*пользователь-агент-строка*, *искать*`)`

**Аргументы**

* *пользователь-агент-строка*: Выражение `string`типа , представляющих строку пользователя-агента.

* *look-for*: Выражение `string` типа `dynamic`или , представляющие то, что функция должна искать в строке пользователя-агента (разбор цели). Возможные варианты: "браузер", "os", "устройство". Если требуется только одна цель разбора, `string` она может быть пройдена по параметру.
Если требуется два или три, `dynamic array`они могут быть переданы как .

**Возвращает**

Объект типа, `dynamic` содержащий информацию о запрашиваемых целях разбора.

Браузер: Семья, MajorVersion, MinorVersion, Патч                 

Операционная система: Семья, MajorVersion, MinorVersion, патч, патчМин             

Устройство: Семья, Бренд, Модель

> [!WARNING]
> Реализация функции построена на регекс-проверках входиной строки на фоне огромного количества предопределенных шаблонов. Поэтому ожидаемое время и потребление процессора высока.
Когда функция используется в запросе, убедитесь, что она работает в распределенном порядке на нескольких машинах.
Если запросы с этой функцией часто используются, вы можете заранее создать результаты с помощью [политики обновления,](../management/updatepolicy.md)но вы должны принять во внимание, что использование этой функции внутри политики обновления увеличит задержку приема.
 
**Пример**

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

Ожидаемый результат – динамический объект:

"Браузер": "Семья": "AdobeAIR", "MajorVersion": "2", "MinorVersion": "5", "Patch": "1"

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

Ожидаемый результат – динамический объект:

"Браузер": "Семья": "Nokia OSS Браузер", "MajorVersion": "3", "MinorVersion": "1", "Patch": "", "Операционная система": "Семья": "Symbian OS", "MajorVersion": "9", "MinorVersion": "2", "Patch": "", "PatchMinor": "", "Устройство": "Семья", ""