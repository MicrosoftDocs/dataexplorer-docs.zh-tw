---
title: 'Azure 資料總管 Kusto EngineV3 (preview) '
description: 深入瞭解 Azure 資料總管 (Kusto) EngineV3
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 133f01498d4cf430d7fdc2649df88186610b3954
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388965"
---
# <a name="enginev3---preview"></a>EngineV3-預覽

Kusto EngineV3 是 Azure 資料總管的新一代儲存體和查詢引擎。 其設計目的是為了提供無與倫比的效能，以擷取和查詢遙測、記錄和時間序列資料。

EngineV3 包含新的優化儲存格式和索引。 EngineV3 使用先進的資料統計資料查詢優化，建立最佳的查詢計劃和即時編譯的查詢執行。 EngineV3 也改善了磁碟快取，以改善目前引擎 (EngineV2) 的大量效能改善查詢效能。 EngineV3 為 Azure 資料總管服務的下一波創新奠定基礎。

在 EngineV3 模式中執行的 Azure 資料總管叢集與 EngineV2 完全相容，因此不需要進行資料移轉。

> [!IMPORTANT]
> EngineV3 將在下列階段提供：
>
> 1. 公開預覽版 (目前的狀態) ：使用者可以在 EngineV3 模式中建立新的叢集。 在公開預覽期間，叢集不受 SLA 約束，且不會針對 Azure 資料總管標記收費。 基礎結構成本會如往常般收費。
> 1. 正式推出 (GA) ：預設會在 EngineV3 模式中建立所有新叢集。 SLA 適用于所有 EngineV3 和 EngineV2 的生產叢集。
> 1. GA 後： EngineV2 上執行的現有工作負載會遷移至 EngineV3。 Azure 資料總管加值費用將會繼續。

## <a name="how-enginev3-works"></a>EngineV3 的運作方式

EngineV3 是另一種與現有資料行存放區平行執行的資料行存放區儲存引擎， (EngineV2) 和資料列存放區 (用於串流內嵌) 。 資料表可以一次合併來自三個存放區的資料，而此「同盟」資料在使用者的觀點中是透明的。

:::image type="content" source="media\engine-v3\engine-v3-architecture.png" alt-text="Azure 資料總管/Kusto EngineV3 架構的示意表示":::

內嵌到資料表中的所有資料會分割成分區，也就是資料表的水準磁區。 每個分區通常會包含一百萬筆記錄，並會與其他分區分開編碼和編制索引。 這項功能可讓引擎以內嵌輸送量達成線性調整。

分區會平均分散在叢集節點上，而這些節點會在本機 SSD 和記憶體中快取。 查詢規劃工具和查詢引擎會準備及執行高度散發和平行查詢，以受益于此分區散發和快取。

EngineV3 著重于將分散式查詢的「下半部」優化。

## <a name="performance"></a>效能

改進的效能和加快的查詢速度來自于引擎的兩項重大變更：

* **全新且改良的分區儲存格式。** 如同 EngineV2，儲存格式是壓縮的資料行存放區，特別注意非結構化 (文字) 和半結構化資料類型。 EngineV3 可改善這些不同資料類型的編碼方式。 索引已經過重新設計，可增加其資料細微性，允許根據索引來評估查詢的部分，而不需要掃描資料。
* **重新設計低層級的分區查詢引擎。** 新的分區查詢會以高效率的方式編譯成高效率的電腦程式代碼，進而產生快速且有效率的融合式查詢評估邏輯。 查詢編譯是由從所有分區收集的資料統計資料來引導，並根據資料行編碼的細節量身打造。

EngineV3 的效能影響取決於所使用的資料集、查詢模式、平行存取和 VM Sku。 在效能測試中，使用的是 100-TB 的資料集，以及涉及分析結構化、非結構化和半結構化資料的不同案例。 使用相同的平行存取層級，以及使用相同的硬體設定，效能改進平均為大約8X。 實際的效能改進會根據查詢和資料集而有所不同。

## <a name="create-an-enginev3-cluster"></a>建立 EngineV3 叢集

若要使用 EngineV3 [建立新](create-cluster-database-portal.md) 的叢集，請在 [叢集建立] 畫面的 [ **基本** ] 索引標籤中，選取 [ **使用引擎 V3 預覽** ] 核取方塊：

:::image type="content" source="media/engine-v3/create-new-cluster-v3.png" alt-text="建立叢集時使用引擎 V3 預覽的核取方塊螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

[使用 Azure 資料總管內嵌資料](ingest-data-overview.md)
