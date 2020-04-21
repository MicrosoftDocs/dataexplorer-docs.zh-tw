---
title: 使用適用於 Power BI 的 Azure 資料資源管理員連接器視覺化資料
description: 在本文中,您將瞭解如何使用 Power BI 中可視化數據的三個選項之一:Azure 資料資源管理器的 Power BI 連接器。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d162adec8ea0c5244fef601bf409d12432f4ce00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501993"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>使用適用於 Power BI 的 Azure 資料總管連接器將資料視覺化

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Power BI 是一個商務分析解決方案，可讓您將資料視覺化並在整個組織共用結果。 Azure 資料總管提供三個選項以便連線到 Power BI 中的資料：使用內建連接器，從 Azure 資料總管匯入查詢，或使用 SQL 查詢。 本文介紹如何使用內置連接器獲取數據並在 Power BI 報表中可視化數據。 使用 Azure 資料資源管理器本機連接器創建 Power BI 儀表板非常簡單。 Power BI 連接器支援[匯入與直接查詢連線模式](https://docs.microsoft.com/power-bi/desktop-directquery-about)。 您可以使用 **「導入**」或 **「直接查詢」** 模式生成儀錶板,具體取決於方案、規模和效能要求。 

## <a name="prerequisites"></a>Prerequisites

完成本文需要以下內容:

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 屬於 Azure Active Directory 成員的組織電子郵件帳戶，以便您連線到 [Azure 資料總管說明叢集](https://dataexplorer.azure.com/clusters/help/databases/samples)。
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (選取 [免費下載]****)

## <a name="get-data-from-azure-data-explorer"></a>從 Azure 資料總管取得資料

首先，連線到 Azure 資料總管說明叢集，然後從 *StormEvents* 資料表帶入資料子集。 [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

1. 在 Power BI Desktop 的 [首頁]**** 索引標籤上，選取 [取得資料]****，然後選取 [更多]****。

    ![取得資料](media/power-bi-connector/get-data-more.png)

1. 搜尋*Azure 資料資源管理員*,選擇**Azure 資料資源管理員**然後**連接**。

    ![搜尋並取得資料](media/power-bi-connector/search-get-data.png)

1. 在**Azure 資料資源管理員(Kusto)** 螢幕上,使用以下資訊填寫表單。

    ![叢集、資料庫、資料表選項](media/power-bi-connector/cluster-database-table.png)

    **設定** | **ReplTest1** | **欄位描述**
    |---|---|---|
    | 叢集 | *https://help.kusto.windows.net* | 說明叢集的 URL。 對於其他群集,URL 以*群\<集\>名稱\<HTTPs://形式。區域\>.kusto.windows.net*。 |
    | 資料庫 | 保留空白 | 裝載於所要連線叢集上的資料庫。 我們會在稍後步驟中選取此項目。 |
    | 資料表名稱 | 保留空白 | 資料庫的其中一個資料表，或是 <code>StormEvents \| take 1000</code>之類的查詢。 我們會在稍後步驟中選取此項目。 |
    | 進階選項 | 保留空白 | 您查詢的選項，例如結果集大小。 |
    | 資料連線模式 | *DirectQuery* | 決定 Power BI 是否匯入資料或直接連線到資料來源。 您可以使用任一選項搭配此連接器。 |
    | | | |
    
    > [!NOTE]
    > 在**導入**模式下,資料將移動到 Power BI。 在**直接查詢**模式下,數據直接從 Azure 數據資源管理器群集查詢。
    >
    > 在 **:**
    > * 您的數據集很小。
    > * 您不需要接近即時數據。 
    > * 您的資料已匯總,或[Kusto 執行聚合](kusto/query/summarizeoperator.md#list-of-aggregation-functions)    
    >
    > 在 **:**
    > * 您的數據集非常大。 
    > * 您需要接近即時的數據。   

1. 如果您還沒有說明叢集的連線，請登入。 使用組織帳戶登入，然後選取 [連線]****。

    ![登入](media/power-bi-connector/sign-in.png)

1. 在 [導覽器]**** 畫面上，展開 **Samples** 資料庫，選取 **StormEvents**，然後 [編輯]****。

    ![選取資料表](media/power-bi-connector/select-table.png)

    資料表會在「Power Query 編輯器」中開啟，您可以先在此編輯器中編輯資料列和資料行，然後再匯入資料。

1. 在 Power Query 編輯器中，選取 **DamageCrops** 資料行旁邊的箭號，然後選取 [遞減排序]****。

    ![DamageCrops 遞減排序](media/power-bi-connector/sort-descending.png)

1. 在 [首頁]**** 索引標籤上，選取 [保留資料列]****，然後 [保留頂端資料列]****。 輸入值 *1000* 可帶入排序資料表的前 1000 個資料列。

    ![保留頂端資料列](media/power-bi-connector/keep-top-rows.png)

1. 在 [首頁]**** 索引標籤上，選取 [關閉並套用]****。

    ![關閉並套用](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>在報告中將資料視覺化

[!INCLUDE [data-explorer-power-bi-visualize-basic](includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>清除資源

如果不再需要為本文創建的報表,請刪除 Power BI 桌面 (.pbix) 檔。

## <a name="next-steps"></a>後續步驟

[使用 Azure 資料資源管理員連接器進行 Power BI 查詢資料的提示](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
