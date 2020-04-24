---
title: Сбой в работе кластера Azure Data Explorer
description: В этой статье описываются действия по устранению неполадок при создании кластера в обозревателе данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501091"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Устранение проблем: Неудачное создание кластера Azure Data Explorer

Маловероятно, что при создании кластера в обозревателе данных Azure произойдет сбой. Но если это все-таки случится, выполните следующие действия.

1. Убедитесь, что у вас есть соответствующие разрешения. Чтобы создать кластер, необходимо быть членом роли *Участник* или *Владелец* в подписке Azure. При необходимости обратитесь к администратору подписки, чтобы он добавил вас к соответствующей роли.

1. Убедитесь в отсутствии ошибок проверки, связанных с именем кластера, введенным в разделе **Создание кластера** на портале Azure.

1. Проверьте [панель мониторинга работоспособности службы Azure](https://azure.microsoft.com/status/). Просмотрите состояние обозревателя данных Azure в регионе, где вы пытаетесь создать кластер.

    Если состояние не **Хорошо** (зеленая галочка), попробуйте создать кластер после улучшения состояния.

1. Если вам нужна помощь в решении проблемы, откройте запрос на поддержку на [портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
