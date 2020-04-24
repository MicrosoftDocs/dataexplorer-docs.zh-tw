---
title: 包含檔案
description: 包含檔案
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
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500368"
---
在本節中,您可以使用 Azure CLI 為本文創建設備標識。 裝置識別碼會區分大小寫。

1. 開啟 [Azure Cloud Shell](https://shell.azure.com/)。

1. 在 Azure 雲外殼中,執行以下指令以安裝 Azure CLI 的 Microsoft Azure IoT 擴展:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. 建立名為`myDeviceId`的新裝置識別,並使用這些指令檢索裝置連接字串:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

記下結果中的裝置連接字串。 裝置應用程式會使用此裝置連接字串，以裝置的形式連接到您的 IoT 中樞。

<!-- images and links -->
