---
title: включить файл
description: включить файл
services: iot-hub
author: orspod
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: orspodek
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e21bd487ff110068ceee4a737a27157068e1f17a
ms.sourcegitcommit: 1c836079c713db3aef33529e6a15af47e2984e7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470632"
---
В этом разделе рассматривается создание удостоверения устройства для этой статьи с помощью Azure CLI. Идентификаторы устройств чувствительны к регистру.

1. Откройте [Azure Cloud Shell](https://shell.azure.com/).

1. В Azure Cloud Shell выполните следующую команду, чтобы установить расширение Интернета вещей Microsoft Azure для Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Создайте удостоверение устройства с именем `myDeviceId` и получите строку подключения устройства с помощью следующих команд:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Запишите строку подключения устройства из результата. Строка подключения этого устройства используется приложением устройства для подключения к Центру Интернета вещей в качестве устройства.

<!-- images and links -->
