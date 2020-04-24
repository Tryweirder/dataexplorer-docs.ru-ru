---
title: Сбои в подключении кластера Azure Data Explorer
description: В этой статье описаны этапы устранения неполадок для подключения к кластеру в Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495371"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Устранение неполадок. Сбой при подключении к кластеру в обозревателе данных Azure

Если вы не можете подключиться к кластеру в обозревателе данных Azure, выполните следующие действия.

1. Строка подключения должна быть указана правильно. Он должен быть в формате: `https://<ClusterName>.<Region>.kusto.windows.net`, как в следующем примере: `https://docscluster.westus.kusto.windows.net`.

1. Убедитесь, что у вас есть соответствующие разрешения. Если этого не сделать, вы получите ответ *несанкционированного*.

    Дополнительные сведения о разрешениях см. в разделе [Управлять разрешениями базы данных](manage-database-permissions.md). При необходимости обратитесь к администратору кластера, чтобы он добавил вас к соответствующей роли.

1. Убедитесь, что кластер не был удален: ознакомьтесь с журналом в вашей подписке.

1. Проверьте [панель мониторинга работоспособности службы Azure](https://azure.microsoft.com/status/). Просмотрите состояние обозревателя данных Azure в регионе, где вы пытаетесь подключиться к кластеру.

    Если статус не **является хорошим** (зеленый чек-метка), попробуйте подключиться к кластеру после улучшения состояния.

1. Если вам нужна помощь в решении проблемы, откройте запрос на поддержку на [портале Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).