---
title: Базы данных - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны базы данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a94b168d8aa8443251f3a01dc659e114b0aacaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509441"
---
# <a name="databases"></a>Базы данных

Kusto следует модели связи хранения данных, где сущность `database`верхнего уровня является . 

Кластер Kusto может размещать несколько баз данных, где каждая база данных будет размещать свою собственную коллекцию [таблиц,](tables.md) [сохраненных функций](stored-functions.md)и [внешних таблиц.](externaltables.md)
Каждая база данных имеет свой собственный набор разрешений, основанный на [модели управления доступом на основе ролей (RBAC)](../../management/access-control/index.md)

**Примечания**  

* Имена баз данных нечувствительны.
* Имена баз данных должны следовать правилам [имен сущностей.](./entity-names.md)
* Максимальный предел баз данных на кластер составляет 10 000.