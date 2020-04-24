---
title: 沙箱 - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的沙箱。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: faa7a8225aecd5992e3064fd10cfcc0e559c5127
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522962"
---
# <a name="sandboxes"></a>沙箱

Kusto 的數據引擎服務能夠運行沙箱以執行需要安全隔離的特定流。
這些流的示例包括使用者定義的腳本,這些文本使用[Python 外掛程式](../query/pythonplugin.md)或[R 外掛程式](../query/rplugin.md)運行。

為了運行這些沙箱,Kusto 使用了微軟[的吊橋](https://www.microsoft.com/research/project/drawbridge/)專案的演變。 其他Microsoft服務使用此解決方案在多租戶環境中運行使用者定義的物件。

在沙箱中運行的流不僅隔離,而且是本地的(靠近數據),這意味著遠端呼叫沒有添加額外的延遲。

## <a name="prerequisites"></a>Prerequisites

* 資料引擎**無法**開啟[磁碟加密](https://docs.microsoft.com/azure/data-explorer/security#data-encryption)。
  * 將來將支持同時運行這兩個功能。
* 執行沙箱需要的套件(影像)部署到資料引擎的每個節點,並且需要專用的 SSD 空間才能執行
  * 估計大小為 20GB,例如,該容量大約是 D14_v2 VM 的 2.5% 的 SSD 容量,即 L16_v1 VM 的 SSD 容量的 0.7%。
  * 這會影響群集的資料容量,因此可能會影響群集[的成本](https://azure.microsoft.com/pricing/details/data-explorer)。

## <a name="runtime"></a>執行階段

* 沙箱查詢運算符可以使用一個或多個沙盒進行執行。
  * 這些僅用於單個運行,不跨多個運行共用,並在運行完成後釋放。
* 每個節點維護預定義的沙箱量,這些沙箱已準備好執行傳入請求。
  * 使用沙箱后,將自動分配一個新的沙盒以替換它。
* 如果沒有可用於為查詢運算符提供服務的預分配沙箱,則將限制它。
  (請參閱[錯誤](#errors),直到分配新的沙箱(每個沙箱最多可能需要 10-15 秒,具體取決於數據節點上的 SKU 和可用資源)。

## <a name="limitations"></a>限制

對於每種沙箱,可以使用群集級[沙箱策略](../management/sandboxpolicy.md)控制其中一些限制。

* **每個節點的沙箱數:** 每個節點的沙箱數量是有限的。
  * 遇到沒有可用沙箱狀態的請求將被限制。
* **網路:** 沙箱無法與 VM 或其外部的任何資源進行交互。
  * 沙箱不能與另一個沙箱交互。
* **CPU:** 沙箱可以使用其主機處理器的最大 CPU 速率是有限的(預設`50%`情況下為 )。
  * 達到此限制時,沙箱的 CPU 使用將受到限制,但執行將繼續。
* **記憶體:** 沙箱可以使用其主機 RAM 的最大 RAM 量受到限制`20GB`(預設情況下為 )。
  * 沙箱終止(以及查詢執行錯誤)將導致達到此限制。
* **磁碟:** 沙箱有一個唯一的目錄附加到它,一邊是,它無法訪問主機的文件系統。
  * 此資料夾提供對與沙箱類型(例如不可自定義的 Python 或 R 包)相匹配的圖像/ 包的訪問。
* **子行程:** 沙箱被阻止生成子進程。

> [!NOTE]
> 沙箱的資源利用率不僅取決於作為請求一部分處理的數據的大小,還取決於在沙箱中運行的邏輯以及沙箱使用的庫的實現。
> (例如,`python`對於`r`與外掛程式,後者表示使用者提供的文稿和它在執行時使用的 Python 或 R 函式庫 )

## <a name="errors"></a>Errors

|程式碼                      |訊息                                                                                        |潛在原因                                                                                                    |
|--------------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|由於限制,沙箱查詢已中止。 在一些退位後重試可能會成功   |目標節點上沒有可用的沙箱。 新的沙箱應在幾秒鐘內可用         |
|E_SB_QUERY_THROTTLED_ERROR|類型"{kind}"的沙箱尚未初始化                                       |沙箱策略最近已更改。 遵守新政策的新沙箱將在幾秒鐘內可用|