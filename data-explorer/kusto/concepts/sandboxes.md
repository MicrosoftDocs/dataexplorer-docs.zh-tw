---
title: 沙箱-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的沙箱。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 5dec95e8d4a73bcff4e8ad037577c51a20fcc64c
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741967"
---
# <a name="sandboxes"></a>沙箱

Kusto 的資料引擎服務能夠執行沙箱，以執行需要安全隔離的特定流程。
這些流程的範例是使用者定義的腳本，會使用[Python 外掛程式](../query/pythonplugin.md)或[R 外掛程式](../query/rplugin.md)來執行。

若要執行這些沙箱，Kusto 會使用 Microsoft 的[drawbridge 為何](https://www.microsoft.com/research/project/drawbridge/)專案進化。 其他 Microsoft 服務會使用此解決方案，在多租使用者環境中執行使用者定義物件。

在沙箱中執行的流程不只會隔離，也會在本機（接近資料），這表示不會針對遠端呼叫加入額外的延遲。

## <a name="prerequisites"></a>先決條件

* 資料**引擎不得啟用**[磁片加密](https://docs.microsoft.com/azure/data-explorer/security#data-encryption)。
  * 未來預期會有並存執行這兩項功能的支援。
* 執行沙箱所需的封裝（映射）會部署到每個資料引擎的節點，並需要專用的 SSD 空間才能執行
  * 估計的大小為 20 gb，例如，大約 2.5% D14_v2 VM 的 SSD 容量，或 L16_v1 VM 的 0.7% SSD 容量。
  * 這會影響叢集的資料容量，因此可能會影響叢集的[成本](https://azure.microsoft.com/pricing/details/data-explorer)。

## <a name="runtime"></a>執行階段

* 沙箱化查詢運算子可能會利用一或多個沙箱來執行。
  * 沙箱僅適用于單一執行，不會在多個回合之間共用，而且會在執行完成後處置一次。
  * 當查詢第一次需要沙箱來執行時，會在節點上延遲初始化沙箱。
    * 這表示第一次執行在節點上使用沙箱的外掛程式時，會包含短的準備期間。
  * 當節點重新開機時（例如，做為服務升級的一部分），會處置其上所有執行中的沙箱。
* 每個節點都會維護預先定義的沙箱量，並準備好執行傳入的要求。
  * 使用沙箱之後，系統會自動設定一個新的沙箱來取代它。
* 如果沒有預先配置的沙箱可用來提供查詢運算子，則會進行節流。
  （請參閱[錯誤](#errors)，直到配置新的沙箱為止（根據資料節點上的 SKU 和可用資源而定，每個沙箱最多可能需要10-15 秒）。

## <a name="limitations"></a>限制

其中一些限制可以使用叢集層級的[沙箱原則](../management/sandboxpolicy.md)來控制，針對每種類型的沙箱。

* **每個節點的沙箱數：** 每個節點的沙箱數目有限。
  * 如果要求遇到沒有可用沙箱的狀態，就會進行節流。
* **網路：** 沙箱無法與 VM 或其外部的任何資源互動。
  * 沙箱無法與另一個沙箱互動。
* **CPU：** 沙箱可以耗用其主機處理器的 CPU 最大速率（預設為`50%`）。
  * 達到此限制時，沙箱的 CPU 使用會進行節流處理，但執行會繼續。
* **記憶體：** 沙箱可以耗用其主機 RAM 的 RAM 數量上限（預設為`20GB`）。
  * 達到此限制會導致沙箱終止（以及查詢執行錯誤）。
* **磁片：** 沙箱具有附加的唯一目錄，旁白，它無法存取主機的檔案系統。
  * 此資料夾可讓您存取符合沙箱種類的影像/套件（例如，不可自訂的 Python 或 R 套件）。
* **子進程：** 沙箱遭到封鎖而無法產生子進程。

> [!NOTE]
> 沙箱的資源使用率不僅取決於在要求中處理的資料大小，也會根據在沙箱中執行的邏輯，以及其所使用的程式庫執行而定。
> （例如，針對`python`和`r`外掛程式，後者表示使用者提供的腳本，以及它在執行時間使用的 Python 或 R 程式庫）

## <a name="errors"></a>Errors

|程式碼                      |訊息                                                                                        |可能的原因                                                                                                    |
|--------------------------|-----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
|E_SB_QUERY_THROTTLED_ERROR|沙箱化查詢因為節流而中止。 在某些輪詢之後重試可能會成功   |目標節點上沒有可用的沙箱。 新的沙箱應該會在幾秒鐘後變成可用         |
|E_SB_QUERY_THROTTLED_ERROR|種類 ' {kind} ' 的沙箱尚未初始化                                       |沙箱原則最近已變更。 新的沙箱遵守新的原則會在幾秒內變成可用|
