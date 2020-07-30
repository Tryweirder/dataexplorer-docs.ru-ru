---
title: parse_user_agent () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается parse_user_agent () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4b4371ee691cea65096cff683a348fcac31e7e48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346411"
---
# <a name="parse_user_agent"></a>parse_user_agent()

Интерпретирует строку агента пользователя, которая определяет браузер пользователя и предоставляет определенные сведения о системе серверам, на которых размещены веб-сайты, посещенные пользователем. Результат возвращается в виде [`dynamic`](./scalar-data-types/dynamic.md) . 

## <a name="syntax"></a>Синтаксис

`parse_user_agent(`*пользователь-агент — строка*, *Поиск*`)`

## <a name="arguments"></a>Аргументы

* *User-Agent-строка*: выражение типа `string` , представляющее строку агента пользователя.

* *Поиск*: выражение типа `string` или `dynamic` , представляющее, что должна искать функция в строке агента пользователя (целевой объект анализа). Возможные варианты: "Browser", "OS", "Device". Если требуется только один целевой объект анализа, ему может быть передан `string` параметр.
Если требуется два или три требования, их можно передать в виде `dynamic array` .

## <a name="returns"></a>Результаты

Объект типа `dynamic` , содержащий сведения о запрошенных целевых объектах анализа.

Браузер: Family, MajorVersion, MinorVersion, исправление                 

Операционная система: семья, MajorVersion, MinorVersion, Patch, Патчминор             

Устройство: семейство, торговая марка, модель

> [!WARNING]
> Реализация функции основана на проверках регулярных выражений входной строки на наличие огромного количества стандартных шаблонов. Поэтому ожидаемое время и потребление ресурсов ЦП высоки.
Если функция используется в запросе, убедитесь, что она выполняется на нескольких компьютерах в распределенном режиме.
Если часто используются запросы с этой функцией, может потребоваться предварительно создать результаты с помощью [политики обновления](../management/updatepolicy.md), но необходимо учитывать, что использование этой функции в политике обновления увеличит задержку приема.
 
## <a name="example"></a>Пример

```kusto
print useragent = "Mozilla/5.0 (Windows; U; en-US) AppleWebKit/531.9 (KHTML, like Gecko) AdobeAIR/2.5.1"
| extend x = parse_user_agent(useragent, "browser") 
```

Ожидаемый результат является динамическим объектом:

{"Browser": {"семейство": "Адобеаир", "MajorVersion": "2", "MinorVersion": "5", "Patch": "1"}}

```kusto
print useragent = "Mozilla/5.0 (SymbianOS/9.2; U; Series60/3.1 NokiaN81-3/10.0.032 Profile/MIDP-2.0 Configuration/CLDC-1.1 ) AppleWebKit/413 (KHTML, like Gecko) Safari/4"
| extend x = parse_user_agent(useragent, dynamic(["browser","os","device"])) 
```

Ожидаемый результат является динамическим объектом:

{"Browser": {"семейство": "Nokia OSS браузер", "MajorVersion": "3", "MinorVersion": "1", "Patch": ""}, "операционная система": {"семейство": "Symbian OS", "MajorVersion": "9", "MinorVersion": "2", "Patch": "", "Патчминор": ""}, "устройство": {"Family": "Nokia N81", "марка": "Nokia", "Model": "N81-3"}}