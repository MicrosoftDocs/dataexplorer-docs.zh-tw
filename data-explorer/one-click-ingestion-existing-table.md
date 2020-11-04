---
title: 在 Azure 資料總管中使用單鍵擷取將 JSON 資料從本機檔案擷取到現有的資料表
description: 只要使用單鍵擷取，即可將資料內嵌 (載入) 到現有 Azure 資料總管資料表。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 41e0e50b2e91280c79941340ebfc855ef6cab27e
ms.sourcegitcommit: f71801764fdccb061f3cf1e3cfe43ec1557e4e0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93293347"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>在 Azure 資料總管中使用單鍵擷取將 JSON 資料從本機檔案擷取到現有的資料表


> [!div class="op_single_selector"]
> * [將 CSV 資料從容器內嵌至新的資料表](one-click-ingestion-new-table.md)
> * [從本機檔案到現有資料表內嵌到 JSON 資料](one-click-ingestion-existing-table.md)

[單鍵擷取](ingest-data-one-click.md)可讓您快速將 JSON、CSV 和其他格式的資料擷取到資料表中，並且輕鬆建立對應結構。 資料可以在一次性或持續擷取程序中，從儲存體、本機檔案或容器中擷取。  

本文件說明如何在特定使用案例中使用直覺式單鍵精靈，將 **JSON** 資料從 **本機檔案** 擷取到 **現有資料表** 中。 可以使用相同的程序並且做些許調整，以涵蓋各種不同的使用案例。

如需單鍵擷取概觀和必要條件清單，請參閱[單鍵擷取](ingest-data-one-click.md)。
針對不同的資料類型或來源，請參閱[在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md)。

## <a name="ingest-new-data"></a>內嵌新資料

在 Web UI 的左側功能表中，以滑鼠右鍵按一下 [資料庫] 或 [資料表]，然後選取 [內嵌新資料]。

   :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="在 Web UI 中選取單鍵擷取":::
 
## <a name="select-an-ingestion-type"></a>選取擷取類型

1. 在 [內嵌新資料] 視窗中，已選取 [來源] 索引標籤。

1. 如果未自動填入 [資料表] 欄位，請從下拉式功能表中選取現有資料表名稱。

    > [!NOTE]
    > 如果您在 [資料表] 資料列上選取 [內嵌新資料]，則所選的資料表名稱會出現在 [專案詳細資料] 中。

1. 在 [擷取類型] 下，執行下列步驟：

   1. **從檔案** 選取  
   1. 選取 [瀏覽] 以找出檔案，或將檔案拖曳到欄位中。
    
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="從檔案單鍵擷取":::

 1. 資料的範例隨即出現。 可以篩選資料，來內嵌以特定字元開頭或結尾的檔案。 

    >[!NOTE] 
    >當您調整篩選條件時，預覽會自動更新。
  
> [!TIP]
> 針對 **從容器** 擷取，請參閱 [在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md#select-an-ingestion-type)

## <a name="edit-the-schema"></a>編輯結構描述

選取 [編輯結構描述] 以查看和編輯資料表資料行設定。

### <a name="map-columns"></a>對應資料行 

1. [對應資料行] 對話方塊隨即開啟。 將一或多個來源資料行或屬性附加至您的 Azure 資料總管資料行。
    * 新的對應會自動設定，或使用現有的對應。 
    * 在 [來源資料行] 欄位中，輸入要與 [目標資料行] 對應的資料行名稱。
    * 若要從對應中刪除資料行，請選取垃圾桶圖示。

      :::image type="content" source="media/one-click-ingestion-existing-table/map-columns.png" alt-text="對應資料行視窗"::: 
    
1. 選取 [更新]。
1. 在 [結構描述] 索引標籤中：
    * 原始程式檔名稱會自動選取 **壓縮類型** 。 在此情況下，壓縮類型為 **JSON**
        
    * 當您選取 [JSON] 時，也必須選取從 1 到 10 的 [JSON 層級]。 這些層級會決定資料表資料行資料分區。

        :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="選取 JSON 層級":::
    
       > [!TIP]
       > 如果您想要使用 **CSV** 檔案，請參閱 [在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md#edit-the-schema)

#### <a name="add-nested-json-data"></a>新增嵌套的 JSON 資料 

若要從 JSON 層級新增資料行，而這些層級不同於上述 所選的主要 **JSON 層級** ，請執行下列步驟：

1. 按一下任何資料行名稱旁的箭號，然後選取 [新的資料行]。

    :::image type="content" source="media/one-click-ingestion-existing-table/new-column.png" alt-text="新增新資料行的螢幕擷取畫面選項 - 一鍵式擷取流程的結構描述索引標籤 - Azure 資料總管":::

1. 輸入新的 **資料行名稱** ，然後從下拉式功能表中選取 [資料行類型]。
1. 在 [來源] 底下，選取 [新建]。

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-source.png" alt-text="螢幕擷取畫面 - 建立新的來源以在一鍵式擷取流程中新增嵌套的 JSON 資料 - Azure 資料總管":::

1. 輸入此資料行的新來源，然後按一下 [確定]。 此來源可以來自任何 JSON 層級。

    :::image type="content" source="media/one-click-ingestion-existing-table/name-new-source.png" alt-text="螢幕擷取畫面 - 用來為新增的資料行命名新資料來源的快顯視窗 - Azure 資料總管一鍵式擷取":::

1. 選取 [建立]  。 您的新資料行會新增至資料表的結尾。

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-column.png" alt-text="螢幕擷取畫面 - 在 Azure 資料總管中按一下 [內嵌] 來建立新的資料行":::

### <a name="edit-the-table"></a>編輯資料表 

將資料內嵌到現有的資料表時，您可能會對資料表所做的變更有所限制。

在資料表中： 
* 選取新的資料行標題以新增 [新的資料行]、[刪除資料行]、[遞增排序] 或 [遞減排序]。 
* 在現有資料行上，只能使用資料排序。

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>開始擷取

選取 [開始擷取] 以建立資料表和對應，以及開始進行資料擷取。

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="開始擷取":::

## <a name="complete-data-ingestion"></a>完成資料擷取

在 [資料擷取已完成] 視窗中，當資料擷取成功完成時，這三個步驟都會標示綠色勾號。

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="單鍵擷取已完成":::

> [!IMPORTANT]
> 若要設定從容器進行連續擷取，請參閱[在 Azure 資料總管中使用單鍵擷取將 CSV 資料從容器擷取到新的資料表](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
