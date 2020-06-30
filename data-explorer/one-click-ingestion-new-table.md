---
title: 在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表
description: 只要使用單鍵擷取，即可將資料內嵌 (載入) 到現有 Azure 資料總管資料表。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 4a390e0501d12b6d70d924e9a5c5576e77cec435
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265291"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表

[單鍵擷取](ingest-data-one-click.md)可讓您快速將 JSON、CSV 和其他格式的資料擷取到資料表中，並且輕鬆建立對應結構。 資料可以在一次性或持續擷取程序中，從儲存體、本機檔案或容器中擷取。  

本文件說明如何在特定使用案例中使用直覺式單鍵精靈，將 **CSV** 資料從**容器**擷取到**新的資料表**中。 您可以使用相同的程序並且做些許調整，以涵蓋各種不同的使用案例。

如需單鍵擷取概觀和必要條件清單，請參閱[單鍵擷取](ingest-data-one-click.md)。
如需將資料內嵌到 Azure 資料總管中現有資料表的詳細資訊，請參 [單鍵擷取至現有資料表](one-click-ingestion-existing-table.md)

## <a name="ingest-new-data"></a>內嵌新資料

1. 在 Web UI 的左側功能表中，以滑鼠右鍵按一下 [資料庫]，然後選取 [內嵌新資料 (預覽)]。

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="擷取新資料":::
 
1. 在 [內嵌新資料 (預覽)] 視窗中，已選取 [來源] 索引標籤。 

1. 選取 [建立新資料表]，然後輸入新資料表的名稱。 您可使用英數位元、連字號和底線。 但不支援萬用字元。

    > [!NOTE]
    > 資料表名稱必須介於 1 到 1024 個字元之間。

:::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="建立新資料表單鍵擷取":::

## <a name="select-an-ingestion-type"></a>選取擷取類型

* 在 [擷取類型] 下，執行下列步驟：
   
  1. **從容器**選取 
  1. 在 [連結至儲存體] 欄位中，新增容器的 [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)，然後選擇性地輸入範例大小。

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="從容器單鍵擷取":::

 > [!TIP] 
 > 若是**從檔案**擷取，請參閱[在 Azure 資料總管中使用單鍵擷取將 JSON 資料從本機檔案擷取到現有的資料表](one-click-ingestion-existing-table.md#select-an-ingestion-type)

* 資料的範例隨即出現。 如果想要的話，您可加以篩選，只擷取以特定字元開頭的檔案。 當您調整篩選條件時，預覽會自動更新。
  
  例如，您可篩選所有以 *data* 開頭並以 *.csv.gz* 副檔名結尾的檔案。

  :::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="單鍵擷取篩選條件":::
  
## <a name="edit-the-schema"></a>編輯結構描述

選取 [編輯結構描述] 以查看和編輯資料表資料行設定。 系統會隨機選取其中一個 Blob，並根據該 Blob 產生結構描述。 服務會藉由查看來源名稱，自動識別其是否壓縮。

### <a name="schema-tab"></a>結構描述索引標籤

1. 在 [結構描述] 索引標籤中：

    * 選取 [資料格式]：

        在此情況下，資料格式為 **CSV**

    > [!TIP]
    > 如果您想要使用 **JSON** 檔案，請參閱[在 Azure 資料總管中使用單鍵擷取將 JSON 資料從本機檔案擷取到現有的資料表](one-click-ingestion-existing-table.md#edit-the-schema)。

    * 您可以選取 [包含資料行名稱] 核取方塊，以忽略檔案的標題列。

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="選取包含資料行名稱":::

1. 在 [對應名稱] 欄位中，輸入對應名稱。 您可使用英數位元和底線。 不支援空格、特殊字元和連字號。

    :::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="資料表對應名稱單鍵擷取":::

### <a name="table"></a>Table

1. 在資料表中： 
    * 按兩下要編輯的新資料行名稱。
    * 選取新的資料行標題並執行下列任何動作：

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

> [!NOTE]
> 若為表格式格式，每個資料行都可以內嵌到 Azure 資料總管中的一個資料行。

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>開始擷取

選取 [開始擷取] 以建立資料表和對應，以及開始進行資料擷取。

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="開始擷取單鍵擷取":::

## <a name="data-ingestion-completed"></a>資料擷取已完成

在 [資料擷取已完成] 視窗中，當資料擷取成功完成時，這三個步驟都會標示綠色勾號。

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="單鍵擷取完成"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="continuous-ingestion---container-only"></a>持續擷取 - 僅限容器

持續擷取可讓您建立事件方格，以接聽來源容器中的新檔案。 任何符合預先定義參數 (前置詞、尾碼等等) 準則的新檔案，都會自動擷取到目的地資料表。 

1. 選取右下角的 [建立持續擷取] 按鈕，以開啟 Azure 入口網站。 資料連線頁面隨即開啟，其中已開啟事件方格資料連接器，並已輸入來源和目標參數 (來源容器、資料表和對應)。
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="持續擷取按鈕":::

1. 選取 [建立] 來建立資料連線，以接聽該容器中的任何變更、更新或新資料。 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="建立事件中樞連線":::

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
