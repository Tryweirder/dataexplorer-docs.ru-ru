---
title: Команды политики регулирования запросов в Azure обозреватель данных
description: В этой статье описываются команды политики регулирования запросов в Azure обозреватель данных
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 85408aadfd15fc48f1e3e86ab01afce5dca15bce
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734020"
---
# <a name="query-throttling-policy-commands"></a>Команды политики регулирования запросов

[Политика регулирования запросов](query-throttling-policy.md) является политикой уровня кластера для ограничения параллелизма запросов в кластере. Для этих команд политики регулирования запросов требуются разрешения [аллдатабасесадмин](../management/access-control/role-based-authorization.md) .

## <a name="query-throttling-policy-object"></a>Объект политики регулирования запросов

В кластере может быть определено ни одной политики регулирования запросов: ноль или одна.

Если в кластере не определена политика регулирования запросов, применяется политика по умолчанию. Дополнительные сведения о политике по умолчанию см. в разделе [ограничения запросов](../concepts/querylimits.md).

|Свойство  |Тип    |Описание                                                       |
|----------|--------|------------------------------------------------------------------|
|IsEnabled |`bool`  |Указывает, включена ли политика регулирования запросов (true) или отключена (false).     |
|макскуантити|`int`|Количество параллельных запросов, которые может запустить кластер. Число должно иметь положительное значение. |

## `.show cluster policy querythrottling`

Возвращает [политику регулирования запросов](query-throttling-policy.md) кластера.

### <a name="syntax"></a>Синтаксис

`.show` `cluster` `policy` `querythrottling`

### <a name="returns"></a>Возвращаемое значение

Возвращает таблицу со следующими столбцами:

|Столбец    |Type    |Описание
|---|---|---
|PolicyName| строка |Имя политики — Куерисроттлингполици.
|EntityName| строка |Empty
|Политика    | строка |Объект JSON, определяющий политику регулирования запросов, отформатированную как [объект политики регулирования запросов](#query-throttling-policy-object)

### <a name="example"></a>Пример

<!-- csl -->
```
.show cluster policy querythrottling 
```

Возвращает:

|PolicyName|EntityName|Политика|чилдентитиес|EntityType|
|---|---|---|---|---|
|куерисроттлингполици||{"Onenableed": true, "Макскуантити": 25}

## `.alter cluster policy querythrottling`

Задает [политику регулирования запросов](query-throttling-policy.md) для указанной таблицы. 

### <a name="syntax"></a>Синтаксис

`.alter``cluster` `policy` `querythrottling` *Куерисроттлингполициобжект*

### <a name="arguments"></a>Аргументы

*Куерисроттлингполициобжект* — это объект JSON, для которого определен объект политики регулирования запросов.

### <a name="returns"></a>Возвращаемое значение

Задает объект политики регулирования запросов кластера, переопределяющий любую текущую политику, а затем возвращает выходные данные соответствующей команды [. отобразить политику `querythrottling` кластера](#show-cluster-policy-querythrottling) .

### <a name="example"></a>Пример

<!-- csl -->
```
.alter cluster policy querythrottling '{"IsEnabled": true, "MaxQuantity": 25}'
```

|PolicyName|EntityName|Политика|чилдентитиес|EntityType|
|---|---|---|---|---|
|куерисроттлингполици||{"Onenableed": true, "Макскуантити": 25}

## `.delete cluster policy querythrottling`

Удаляет объект [политики регулирования запросов](query-throttling-policy.md) кластера.

### <a name="syntax"></a>Синтаксис

`.delete` `cluster` `policy` `querythrottling`
