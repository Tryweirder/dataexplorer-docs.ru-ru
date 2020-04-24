---
title: включить файл
description: включить файл
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500376"
---
В этом разделе используется Azure CLI для создания итога устройства для этой статьи. Идентификаторы устройств чувствительны к регистру.

1. Откройте [Azure Cloud Shell](https://shell.azure.com/).

1. В Azure Cloud Shell запустите следующую команду для установки расширения Microsoft Azure IoT для Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Создайте новую `myDeviceId` идентификацию устройства, называемую и извлекайте строку соединения устройства с помощью этих команд:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Запишите строку подключения устройства из результата. Строка подключения этого устройства используется приложением устройства для подключения к Центру Интернета вещей в качестве устройства.

<!-- images and links -->
