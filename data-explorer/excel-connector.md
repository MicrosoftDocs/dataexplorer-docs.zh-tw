---
title: 使用 Microsoft Excel 的 Azure 資料資源管理員連接器視覺化資料
description: 在本文中,您將瞭解如何使用 Azure 資料資源管理器的 Excel 連接器。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499679"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>使用 Excel 的 Azure 資料資源管理員連接器視覺化資料

Azure 資料資源管理員提供了兩個用於連接到 Excel 中的數據的選項:使用本機連接器或從 Azure 數據資源管理器導入查詢。 本文介紹如何在 Excel 中使用本機連接器並連接到 Azure 數據資源管理器群集來獲取和可視化數據。

Azure 資料資源管理器 Excel 本機連接器提供了將查詢結果匯出到 Excel 的能力。 您還可以將 KQL 查詢添加為 Excel 資料來源,用於其他計算或可視化效果。

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>將 Kusto 查詢定義為 Excel 資料來源,並將資料載入 Excel

1. 開啟**Microsoft Excel**。
1. 在「**資料**」選項卡中,選擇**從 Azure** > **資料資源管理員****取得資料** > 。

    ![從 Azure 資料總管取得資料](media/excel-connector/get-data-from-adx.png)

1. 在**Azure 資料資源管理員 (Kusto)** 視窗中,完成以下欄位並選擇 **「確定**」。

    ![Azure 資料資源管理員 (函式庫托)視窗](media/excel-connector/adx-connection-window.png)
    
    |欄位   |描述 |
    |---------|---------|
    |**叢集**   |   叢集名稱(必要)      |    
    |**Database**     |    資料庫名稱      |    
    |**表格名稱或 Azure 資料資源管理員查詢**    |     表或 Azure 資料資源管理員查詢的名稱    | 
    
    **進階選項:**

     |欄位   |描述 |
    |---------|---------|
    |**限制查詢結果記錄編號**     |     限制載入 Excel 中的紀錄  |    
    |**限制查詢結果資料大小(位元組)**    |    限制資料大小      |   
    |**關閉結果集截斷**    |         |      
    |**其他設定語句(以分號分隔)**    |    新增`set`要套用資料來源的敘述     |   

1.  在 **"導航器"** 窗格中,導航到正確的表。 在「表預覽」窗格中,選擇 **「轉換資料**」以更改資料或選擇 **「載入**」以將其載入到 Excel。

![表預覽視窗](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > 如果已指定**資料庫**和/或**表名稱或 Azure 資料資源管理員查詢**,則將自動打開正確的表預覽窗格。 

## <a name="analyze-and-visualize-data-in-excel"></a>在 Excel 中分析及視覺化資料

一旦數據載入 Excel 工作表中,並且在 Excel 工作表中可用,您可以透過建立關係和視覺物件來分析、匯總和可視化資料。 

1.  在**表格設計選項**卡中,選擇 **「使用資料透視表進行匯總**」。 在 **"創建資料透視表"** 視窗中,選擇相關表,然後選擇 **「確定**」。

    ![建立資料透視表](media/excel-connector/create-pivot-table.png)

1. 在 **「資料透視表欄位」** 窗格中,選擇相關表列以創建摘要表。 在下面的範例中,選擇了**EventId** **和狀態**。
    
    ![選擇資料透視表欄位](media/excel-connector/pivot-table-pick-fields.png)

1. 在 **「資料透視表分析」** 選項卡中,選擇**數據透視表**以基於表創建視覺物件。 

    ![樞紐分析](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. 在下面的範例中,使用**事件ID、****開始時間和****事件類型**來查看有關天氣事件的其他資訊。

    ![顯現資料](media/excel-connector/visualize-excel-data.png)

1. 創建完整的儀錶板以監視數據。

## <a name="next-steps"></a>後續步驟

[使用匯入到 Microsoft Excel 的 Azure 資料資源管理員庫斯托查詢視覺化資料](excel-blank-query.md)