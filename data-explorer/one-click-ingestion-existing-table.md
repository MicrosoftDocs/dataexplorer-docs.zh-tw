---
title: 在 Azure 資料總管中使用單鍵擷取將 JSON 資料從本機檔案擷取到現有的資料表
description: 只要使用單鍵擷取，即可將資料內嵌 (載入) 到現有 Azure 資料總管資料表。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 422c930835e4ebe68cb59b27e51934d800fe14c1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265147"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>在 Azure 資料總管中使用單鍵擷取將 JSON 資料從本機檔案擷取到現有的資料表

[單鍵擷取](ingest-data-one-click.md)可讓您快速將 JSON、CSV 和其他格式的資料擷取到資料表中，並且輕鬆建立對應結構。 資料可以在一次性或持續擷取程序中，從儲存體、本機檔案或容器中擷取。  

本文件說明如何在特定使用案例中使用直覺式單鍵精靈，將 **JSON** 資料從**本機檔案**擷取到**現有資料表**中。 您可以使用相同的程序並且做些許調整，以涵蓋各種不同的使用案例。

如需單鍵擷取概觀和必要條件清單，請參閱[單鍵擷取](ingest-data-one-click.md)。
針對不同的資料類型或來源，請參閱[在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md)。

## <a name="ingest-new-data"></a>內嵌新資料

1. 在 Web UI 的左側功能表中，以滑鼠右鍵按一下 [資料庫] 或 [資料表]，然後選取 [內嵌新資料 (預覽)]。

    :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="在 Web UI 中選取單鍵擷取":::
 
## <a name="select-an-ingestion-type"></a>選取擷取類型

1. 在 [內嵌新資料 (預覽)] 視窗中，已選取 [來源] 索引標籤。

1. 如果未自動填入 [資料表] 欄位，請從下拉式功能表中選取現有資料表名稱。

    > [!NOTE]
    > 如果您在 [資料表] 資料列上選取 [內嵌新資料 (預覽)]，則所選的資料表名稱會出現在 [專案詳細資料] 中。

1. 在 [擷取類型] 下，執行下列步驟：

   1. **從檔案**選取  
   1. 選取 [瀏覽] 以找出檔案，或將檔案拖曳到欄位中。
    * 資料的範例隨即出現。 如果想要的話，您可加以篩選，只擷取以特定字元開頭的檔案。 
    >[!NOTE] 
    >當您調整篩選條件時，預覽會自動更新。
  
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="從檔案單鍵擷取":::

 > [!TIP]
 > 針對**從容器**擷取，請參閱[在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md#select-an-ingestion-type)

## <a name="edit-the-schema"></a>編輯結構描述

選取 [編輯結構描述] 以查看和編輯資料表資料行設定。

### <a name="map-columns"></a>對應資料行 

1. [對應資料行] 對話方塊隨即開啟，您可以將一或多個來源資料行或屬性附加至您的 Azure 資料總管資料行。
    * 系統會自動設定新的對應。 您也可以使用現有對應。 
    * 在 [來源資料行] 欄位中，輸入要與 [目標資料行] 對應的資料行名稱。
    * 若要從對應中刪除資料行，請選取垃圾桶圖示。

    :::image type="content" source="media/one-click-ingestion-existing-table/map-columns.png" alt-text="對應資料行視窗"::: 
    
1. 選取 [更新]。
1. 在 [結構描述] 索引標籤中：
    * 原始程式檔名稱會自動選取**壓縮類型**。 在此情況下，壓縮類型為 **JSON**
        
    * 當您選取 [JSON] 時，也必須選取從 1 到 10 的 [JSON 層級]。 這些層級會決定資料表資料行資料分區。

    :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="選取 JSON 層級":::
    
    > [!TIP]
    > 如果您想要使用 **CSV** 檔案，請參閱[在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md#edit-the-schema)

### <a name="table"></a>Table 

在資料表中： 
    * 選取新的資料行標題以新增 [新的資料行]、[刪除資料行]、[遞增排序] 或 [遞減排序]。 在現有資料行上，只能使用資料排序。

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>開始擷取

選取 [開始擷取] 以建立資料表和對應，以及開始進行資料擷取。

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="開始擷取":::

## <a name="data-ingestion-completed"></a>資料擷取已完成

在 [資料擷取已完成] 視窗中，當資料擷取成功完成時，這三個步驟都會標示綠色勾號。

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="單鍵擷取已完成":::

> [!IMPORTANT]
> 如果您想要設定從容器進行持續擷取，請參閱[在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md#continuous-ingestion---container-only)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
