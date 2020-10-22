---
title: 包含檔案
description: 包含檔案
author: orspod
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: orspodek
ms.custom: include file
ms.openlocfilehash: ea41eaf8a24bdff56c99092f6a862393eb5272ab
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347007"
---
本節將說明如何使用 [Azure 入口網站](https://portal.azure.com)建立 IoT 中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從 Azure 首頁中選擇 [+建立資源] 按鈕，然後在 [搜尋 Marketplace] 欄位中輸入「IoT 中樞」。

1. 從搜尋結果中選取 [IoT 中樞]，然後選取 [建立]。

1. 在 [基本資料] 索引標籤上，完成如下所示的欄位：

   - 訂用帳戶：選取要為您的中樞使用的訂用帳戶。

   - **資源群組**：選取資源群組或建立新的資源群組。 若要建立新的資源群組，選取 [新建] 並填入您要使用的名稱。 若要使用現有資源群組，請選取該資源群組。 如需詳細資訊，請參閱[管理 Azure Resource Manager 資源群組](/azure/azure-resource-manager/management/manage-resource-groups-portal)。

   - **區域**：選取您要放置中樞的區域。 選取最靠近您的位置。 某些功能 (例如 [IoT 中樞裝置串流](/azure/iot-hub/iot-hub-device-streams-overview)) 僅適用於特定區域。 針對這些有限的功能，您必須選取其中一個支援的區域。

   - **IoT 中樞名稱**：輸入您的中樞名稱。 此名稱必須是全域唯一的。 如果您輸入的名稱可用，則會出現綠色核取記號。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![在 Azure 入口網站中建立中樞](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. 完成時，選取 [下一步:大小與級別] 繼續建立中樞。

   ![使用 Azure 入口網站設定新中樞的大小與級別](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   您可以在這裡接受預設設定。 若有需要，您可以修改下列任一欄位： 

    - **定價與級別層**：您選取的階層。 您可以依據所需的功能多寡，以及每天透過解決方案傳送的訊息多寡，從數個層級中做選擇。 免費層適用於測試和評估。 它可允許 500 個裝置連接到中樞，每天最多可允許 8,000 則訊息。 每個 Azure 訂用帳戶可以在免費層建立一個 IoT 中樞。 

      如果您正在逐步執行 IoT 中樞裝置串流的快速入門，請選取免費層。

    - **IoT 中樞單位**：每天每單位允許的訊息數目取決於您的中樞定價層。 例如，如果您想要中樞支援 700,000 封訊息的輸入，您可以選擇 2 個 S1 層單位。
    如需有關其他層級選項的詳細資料，請參閱[選擇適合的 IoT 中樞層](/azure/iot-hub/iot-hub-scaling)。

    - **Azure 資訊安全中心**：開啟此功能可為 IoT 和您的裝置新增額外的威脅防護層。 免費層中的中樞無法使用此功能選項。 如需詳細資訊，請參閱[適用於 IoT 的 Azure 資訊安全中心](/azure/asc-for-iot/)。

    - **進階設定** > **裝置到雲端的分割區**：此屬性會將裝置到雲端的訊息數與同時閱讀訊息的讀者數產生關聯。 大部分的中樞只需要四個分割區。

1.  完成時，選取 下一步:**標籤** 以繼續進入下一個畫面。

    標籤為名稱/值組。 您可以將相同標記指派給多個資源和資源群組，以將資源分類並合併計費。 如需詳細資訊，請參閱 [使用標記組織您的 Azure 資源](/azure/azure-resource-manager/management/tag-resources)。

    ![使用 Azure 入口網站為中樞指派標籤](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  完成時，選取 [下一步:檢閱 + 建立] 以檢閱您的選擇。 您會看到類似此畫面的內容，但使用您在建立中樞時所選取的值。 

    ![檢閱有關建立新中樞的資訊](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  選取 [建立] 以建立新的中樞。 建立中樞需要幾分鐘的時間。