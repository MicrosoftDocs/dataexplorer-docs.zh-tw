---
title: 在 Azure 資料總管中建立資料表
description: 瞭解如何使用單鍵體驗，輕鬆地在 Azure 資料總管中建立資料表。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/06/2020
ms.openlocfilehash: 3302e7cb94a2184be64664f3c3d8698b8bea7643
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402732"
---
# <a name="create-a-table-in-azure-data-explorer-preview"></a>在 Azure 資料總管 (preview 中建立資料表) 

建立資料表是在 Azure 資料總管中進行 [資料](ingest-data-overview.md) 內嵌和 [查詢](write-queries.md) 程式的重要步驟。 在 [Azure 資料總管中建立叢集和資料庫](create-cluster-database-portal.md)之後，您就可以建立資料表。 下列文章說明如何使用 Azure 資料總管 Web UI，快速且輕鬆地建立資料表和架構的對應。 

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立 [Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)。
* 登入 [Azure 資料總管 Web UI](https://dataexplorer.azure.com/) 並[將連線新增至您的叢集](web-query-data.md#add-clusters)。

## <a name="create-a-table"></a>建立資料表

1. 在 Web UI 的左側功能表中，以滑鼠右鍵按一下 **ExampleDB** ，也就是您的資料庫名稱，然後選取 [ **建立資料表 (預覽]) **。

    :::image type="content" source="./media/one-click-table/create-table.png" alt-text="在 Azure 資料總管 Web UI 中建立資料表":::

[ **建立資料表** ] 視窗隨即開啟，並選取 [ **來源** ] 索引標籤。
1. 資料庫 **欄位會** 自動填入您的資料庫。 您可以從下拉式功能表中選取不同的資料庫。
1. 在 [ **資料表名稱**] 中，輸入資料表的名稱。 
    > [!TIP]
    >  資料表名稱最多可有1024個字元，包括英數位元、連字號和底線。 但不支援萬用字元。

### <a name="select-source-type"></a>選取來源類型

1. 在 [ **來源類型**] 中，選取您要用來建立資料表對應的資料來源。 從下列選項中選擇： **從 blob**、 **檔案**或 **容器**。
   
    
    * 如果您使用的是 **容器**：
        * 輸入 blob 的儲存體 url，並選擇性地輸入範例大小。 
        * 使用檔案 **篩選**器篩選您的檔案。 
        * 選取將在下一個步驟中用來定義架構的檔案。

        :::image type="content" source="media/one-click-table/storage.png" alt-text="使用 blob 建立資料表以建立架構對應":::
    
    * 如果您使用的是 **本機**檔案：
        * 選取 [瀏覽] 以找出檔案，或將檔案拖曳到欄位中。

        :::image type="content" source="./media/one-click-table/data-from-file.png" alt-text="根據本機檔案中的資料建立資料表 ":::

    * 如果您使用的是 **blob**：
        * 在 [ **儲存體的連結** ] 欄位中，新增容器的 [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) ，並選擇性地輸入範例大小。 

1. 選取 [ **編輯架構** ] 以繼續前往 [ **架構** ] 索引標籤。

### <a name="edit-schema"></a>編輯架構

在 [ **架構** ] 索引標籤中，系統會在左側窗格中自動識別您的 [資料格式](ingest-data-one-click.md#file-formats) 和壓縮。 如果識別不正確，請使用 [ **資料格式** ] 下拉式功能表來選取正確的格式。

   * 如果您的資料格式為 JSON，您也必須選取 JSON 層級，從1到10。 這些層級會決定資料表資料行資料分區。
   * 如果您的資料格式為 CSV，請選取核取方塊， **其中包含** 資料行名稱，以忽略檔案的標題列。

        :::image type="content" source="./media/one-click-table/schema-tab.png" alt-text="在 Azure 資料總管中按一下 [建立資料表] 中的 [編輯架構] 索引標籤":::
 
1. 在 [ **對應**] 中，輸入此資料表之架構對應的名稱。 
    > [!TIP]
    >  資料表名稱可以包含英數位元和底線。 不支援空格、特殊字元和連字號。
1. 選取 [建立]  。

## <a name="create-table-completed-window"></a>建立資料表完成視窗

在 [ **建立資料表完成** ] 視窗中，當資料表建立成功完成時，這兩個步驟都會標示綠色核取記號。

* 選取 [ **View 命令** ] 以開啟每個步驟的編輯器。 
    * 在編輯器中，您可檢視及複製從您的輸入產生的自動命令。
    
    :::image type="content" source="./media/one-click-table/table-completed.png" alt-text="建立資料表時，在單一點擊體驗中完成資料表建立-Azure 資料總管":::
 
在 [ **建立資料表** 進度] 下方的圖格中，探索 **快速查詢** 或 **工具**：

* [快速查詢] 包含具有範例查詢的 Web UI 連結。

* **工具** 包含可 **復原** 執行相關 drop 命令的連結。

> [!NOTE]
> 當您使用時，可能會遺失資料。<br>
> 此工作流程中的 drop 命令只會還原 create table process (new table 和 schema mapping) 所做的變更。

## <a name="next-steps"></a>後續步驟

* [資料擷取概觀](ingest-data-overview.md)
* [單鍵擷取](ingest-data-one-click.md)
* [撰寫 Azure 資料總管的查詢](write-queries.md)  
