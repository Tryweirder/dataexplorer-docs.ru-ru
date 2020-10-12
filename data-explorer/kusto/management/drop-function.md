---
title: . Drop-функция — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается функция Drop в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 279af228d15f511b35c26eebd0d21521450be786
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954745"
---
# <a name="drop-function"></a>.drop function

Удаляет функцию из базы данных.
Сведения об удалении нескольких функций из базы данных см. в разделе [. Drop functions](#drop-functions).

**Синтаксис**

`.drop``function` *FunctionName* [ `ifexists` ]

* `ifexists`: Если указано, изменяет поведение команды для неудачи для несуществующей функции.

> [!NOTE]
> * Требуется [разрешение функции "Администратор"](../management/access-control/role-based-authorization.md).
    
|Выходной параметр |Тип |Описание
|---|---|--- 
|name  |Строка |Имя удаленной функции
 
**Пример** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>. Drop, функции

Удаляет из базы данных несколько функций.

**Синтаксис**

`.drop``functions`(*FunctionName1*, *FunctionName2*,..) [IFEXISTS]

**Возвращает**

Эта команда возвращает список оставшихся функций в базе данных.

|Выходной параметр |Тип |Описание
|---|---|--- 
|name  |Строка |Имя функции. 
|Параметры  |Строка |Параметры, необходимые для функции.
|Текст  |Строка |(Ноль или более) `let` инструкции, за которыми следует допустимое выражение CSL, которое вычисляется при вызове функции.
|Папка|Строка|Папка, используемая для классификации функций пользовательского интерфейса. Этот параметр не изменяет способ вызова функции.
|DocString|Строка|Описание функции для целей пользовательского интерфейса.

Требуется [разрешение функции "Администратор"](../management/access-control/role-based-authorization.md).

**Пример** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
