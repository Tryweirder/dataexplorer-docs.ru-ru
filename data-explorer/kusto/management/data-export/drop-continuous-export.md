---
title: Удаление непрерывной экспорта данных с обозреватель данных Azure
description: В этой статье описывается, как удалить непрерывный экспорт данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: f08d8c99c242aa63e3847d3965bf9511967b60e1
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149396"
---
# <a name="drop-continuous-export"></a>Удалить непрерывный экспорт

Удаляет задание непрерывного экспорта.

## <a name="syntax"></a>Синтаксис

`.drop``continuous-export` *Континуаусекспортнаме*

## <a name="properties"></a>Свойства

| Свойство             | Тип   | Описание                |
|----------------------|--------|----------------------------|
| континуаусекспортнаме | Строка | Имя непрерывного экспорта |

## <a name="output"></a>Выходные данные

Оставшиеся непрерывные операции экспорта в базе данных (после удаления). Выходная схема, как в [команде "показывать непрерывный экспорт](show-continuous-export.md)".
