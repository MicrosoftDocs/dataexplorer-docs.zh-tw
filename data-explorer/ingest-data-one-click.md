---
title: 使用單鍵擷取將資料內嵌到 Azure 資料總管資料
description: 使用單鍵擷取輕鬆將資料內嵌 (載入) 到 Azure 資料總管的概觀。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 98cfbf8b196d0496b4c7e86b03d6d2787ba6919f
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188450"
---
# <a name="what-is-one-click-ingestion"></a>什麼是單鍵擷取？

單鍵擷取可讓資料擷取程序變得簡單、快速且直覺化。 單鍵擷取可協助您快速開始擷取資料、建立資料庫資料表、對應結構。 從不同資料格式的不同類型來源中選取資料，不論是一次性或連續擷取程序。

下列功能是單鍵擷取如此實用的原因：

* 擷取精靈引導的直覺式體驗
* 只需要幾分鐘的時間就能擷取資料
* 從不同類型的來源擷取資料：本機檔案、Blob 和容器 (最多 10000 個 Blob)
* 以各種不同的[格式](#file-formats)擷取資料
* 將資料擷取至新的或現有的資料表
* 資料表對應和結構描述只是建議，您可以輕易變更
* 使用事件方格，輕鬆快速地從容器中繼續擷取

第一次內嵌資料時，或不熟悉資料的結構描述時，單鍵擷取特別有用。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立 [Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)。
* 登入 [Azure 資料總管 Web UI](https://dataexplorer.azure.com/) 並[將連線新增至您的叢集](web-query-data.md#add-clusters)。

## <a name="ingest-new-data"></a>內嵌新資料

單鍵擷取精靈會引導您完成單鍵擷取流程。

* 若要從叢集的 [歡迎使用 Azure 資料總管] 主畫面中存取單鍵擷取精靈，請完成前兩個步驟 ([建立叢集和建立資料庫](#prerequisites))，然後選取 [擷取新資料]。

    :::image type="content" source="media/ingest-data-one-click/welcome-ingestion.png" alt-text="從 [歡迎使用 Azure 資料總管] 擷取新資料":::

* 若要從 [Azure 資料總管 Web UI](https://dataexplorer.azure.com/) 存取精靈，請以滑鼠右鍵按一下 Azure 資料總管 Web UI 左側功能表中的**資料庫**或**資料表**列，然後選取 [擷取新資料 (預覽)]。

    :::image type="content" source="media/ingest-data-one-click/one-click-ingestion-in-webui.png" alt-text="在 Web UI 中選取單鍵擷取":::

<!-- TODO either change the local file tutorial to blob storage or create another one to show users how to do this-->

## <a name="one-click-ingestion-wizard"></a>單鍵擷取精靈

> [!NOTE]
> 本節約略說明精靈內容。 您選取的選項取決於您所擷取的資料格式、您擷取的資料來源種類，以及要擷取到新的或現有的資料表。
>
> 如需範例案例，請參閱：
> * [使用 CSV 格式從容器擷取到新的資料表](one-click-ingestion-new-table.md)
> * [使用 JSON 格式從本機檔案擷取到現有的資料表](one-click-ingestion-existing-table.md) 

此精靈會引導您從下列選項中選取：
   * 內嵌到[現有的資料表](one-click-ingestion-existing-table.md)
   * 內嵌到[新的資料表](one-click-ingestion-new-table.md)
   * 內嵌資料來源：
      * Blob 儲存體
      * [本機檔案](one-click-ingestion-existing-table.md)
      * [容器](one-click-ingestion-new-table.md)


### <a name="schema-mapping"></a>結構描述對應

服務會自動產生結構描述和擷取屬性，您可以加以變更。 根據您要擷取到新的或現有的資料表而定，您可以使用現有的對應結構，或建立一個新的對應結構。

在 [結構描述] 索引標籤中，可以執行下列動作：
   * 確認自動產生的壓縮類型。
   * 選擇[資料的格式](#file-formats)。 不同的格式可讓您進行進一步的變更。

#### <a name="file-formats"></a>檔案格式

單鍵擷取支援以下列任何格式從來源資料內嵌新的資料表：
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

### <a name="editor-window"></a>編輯器視窗

在 [編輯器] 視窗中，您可以視需要調整資料表資料行。 

|資料表類型  |可用的資料行調整  |
|---------|---------|
|新增     | 新增資料行、刪除資料行、遞增排序、遞減排序  |
|Existing     | 新增資料行、遞增排序、遞減排序  |

>[!NOTE]
> 您隨時都可以在 [編輯器] 窗格上方，開啟[命令編輯器](one-click-ingestion-new-table.md#command-editor)。 在命令編輯器中，您可以檢視及複製從您的輸入產生的自動命令。

### <a name="data-ingestion"></a>資料擷取

當您完成結構描述對應和資料行操作之後，擷取精靈就會啟動資料擷取程序。 

* 從 **non-container** 來源內嵌資料時，擷取會立即生效。

* 如果您的資料來源是**容器**：
    * Azure 資料總管的[批次處理原則](kusto/management/batchingpolicy.md)會彙總您的資料。 
    * 擷取之後，您可以下載擷取報告，並檢閱每個已解決 Blob 的效能。 
    * 您可以選取**建立連續擷取**，並且設定[使用事件方格進行連續擷取](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)。
 
### <a name="initial-data-exploration"></a>初始資料探索
   
擷取之後，精靈會提供您使用 **[快速命令](one-click-ingestion-existing-table.md#quick-queries-and-tools)** 的選項，以進行資料的初始探索。

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管中使用單鍵擷取將 JSON 資料從本機檔案擷取到現有的資料表](one-click-ingestion-existing-table.md)
* [在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md)
* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
