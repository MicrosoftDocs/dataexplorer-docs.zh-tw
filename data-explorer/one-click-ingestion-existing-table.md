---
title: 使用單鍵擷取將資料內嵌到現有 Azure 資料總管資料表
description: 只要使用單鍵擷取，即可將資料內嵌 (載入) 到現有 Azure 資料總管資料表。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a26e48520f5cc577a52f877da16996df0d03d2b8
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374368"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>使用單鍵擷取將資料內嵌到 Azure 資料總管中的現有資料表

單鍵擷取可讓您快速將 JSON、CSV 和其他格式的資料內嵌到資料表中。 使用 Azure 資料總管 Web UI，即可從儲存體、本機檔案或容器內嵌資料。 

本文件說明如何使用直覺式單鍵精靈，將檔案中的 JSON 資料內嵌到現有資料表中。 接著，您可使用 Azure 資料總管 Web UI 來編輯資料表和執行查詢。

第一次內嵌資料時，或不熟悉資料的結構描述時，單鍵擷取特別有用。 

如需單鍵擷取概觀和必要條件清單，請參閱[單鍵擷取](ingest-data-one-click.md)。
如需將資料內嵌到 Azure 資料總管中新資料表的詳細資訊，請參 [單鍵擷取至新的資料表](one-click-ingestion-new-table.md)

## <a name="ingest-new-data"></a>內嵌新資料

1. 在 Web UI 的左側功能表中，以滑鼠右鍵按一下 [資料庫]  或 [資料表]  ，然後選取 [內嵌新資料 (預覽)]  。

    ![在 Web UI 中選取單鍵擷取](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. 在 [內嵌新資料 (預覽)]  視窗中，已選取 [來源]  索引標籤。

1. 如果未自動填入 [資料表]  欄位，請從下拉式功能表中選取現有資料表名稱。
    > [!TIP]
    > 如果您在 [資料表]  資料列上選取 [內嵌新資料 (預覽)]  ，則所選的資料表名稱會出現在 [專案詳細資料]  中。

[!INCLUDE [data-explorer-one-click-ingestion-types](includes/data-explorer-one-click-ingestion-types.md)]
    
選取 [編輯結構描述]  以查看和編輯資料表資料行設定。

## <a name="edit-the-schema"></a>編輯結構描述

1. [對應資料行]  對話方塊隨即開啟，而您可將來源資料行對應至目標資料表資料行。 
    * 在 [來源資料行]  欄位中，輸入要與 [目標資料行]  對應的資料行名稱。
    * 若要從對應中刪除資料行，請選取垃圾桶圖示。

    ![對應資料行視窗](media/one-click-ingestion-existing-table/map-columns.png)

1. 選取 [更新]  。
1. 在 [結構描述]  索引標籤中：
    1. 原始程式檔名稱會自動選取**壓縮類型**。

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. 如果您選取 [JSON]  ，則也必須選取從 1 到 10 的 [JSON 層級]  。 這些層級會影響資料表資料行的資料描述。

    ![選取 JSON 層級](media/one-click-ingestion-existing-table/json-levels.png)

    * 針對表格式格式，則可選取 [包含資料行名稱]  核取方塊，以忽略檔案的標題列。
        
    ![選取 [包含資料行名稱]](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > 表格式格式只能將資料行資料內嵌到 Azure 資料總管資料表中的一個資料行。 

    * 新的對應會自動設定，但您可將其變更為使用現有對應。 
    * 選取 [對應資料行]  按鈕以開啟 [對應資料行]  視窗。 在 [對應資料行]  視窗中，您可以將一或多個來源資料行或屬性附加至您的 Azure 資料總管資料行。

## <a name="copy-and-paste-queries"></a>複製並貼上查詢

1. 在 [編輯器]  窗格上方，選取 **v** 按鈕以開啟編輯器。 在編輯器中，您可檢視及複製從您的輸入產生的自動命令。 
1. 在資料表中： 
    * 選取新的資料行標題以新增 [新的資料行]  、[刪除資料行]  、[遞增排序]  或 [遞減排序]  。 在現有資料行上，只能使用資料排序。

    > [!Note]
    > * 您無法更新現有資料表的名稱和資料類型。
    > * Drop 命令只會還原其擷取流程所做的變更 (新的範圍和資料行)。 不會捨棄任何其他項目。

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>開始擷取

選取 [開始擷取]  以建立資料表和對應，以及開始進行資料擷取。

![開始擷取](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>資料擷取已完成

在 [資料擷取已完成]  視窗中，如果資料擷取成功完成，這三個步驟都會標示綠色勾號。
 
![單鍵資料擷取完成](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
