---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: orspod
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: orspodek
ms.custom: include file
ms.openlocfilehash: 8b6426c96070433ba489cc7579cc5168b25dce62
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375194"
---
在本節中，您會使用 Azure CLI 建立本文中的裝置身分識別。 裝置識別碼會區分大小寫。

1. 開啟 [Azure Cloud Shell](https://shell.azure.com/)。

1. 在 Azure Cloud Shell 中執行下列命令，以安裝適用於 Azure CLI 的 Microsoft Azure IoT 延伸模組：

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. 建立稱為 `myDeviceId` 的新裝置身分識別，並使用下列命令擷取裝置連接字串：

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

記下結果中的裝置連接字串。 裝置應用程式會使用此裝置連接字串，以裝置的形式連接到您的 IoT 中樞。

<!-- images and links -->
