---
title: 使用適用于 Power BI 的 Azure 資料總管連接器將資料視覺化
description: 在本文中，您將瞭解如何使用三個選項中的其中一個來視覺化 Power BI 中的資料：適用于 Azure 資料總管的 Power BI 連接器。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: d02f9732791bf66a488779e2bc413fa441664ef7
ms.sourcegitcommit: f134d51e52504d3ca722bdf6d33baee05118173a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563322"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>使用適用於 Power BI 的 Azure 資料總管連接器將資料視覺化

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Power BI 是一個商務分析解決方案，可讓您將資料視覺化並在整個組織共用結果。 Azure 資料總管提供三個選項以便連線到 Power BI 中的資料：使用內建連接器，從 Azure 資料總管匯入查詢，或使用 SQL 查詢。 本文說明如何使用內建連接器來取得資料，並在 Power BI 報表中將其視覺化。 使用 Azure 資料總管 native connector 來建立 Power BI 儀表板很簡單。 Power BI 連接器支援匯 [入和直接查詢連接模式](/power-bi/desktop-directquery-about)。 您可以使用 [匯 **入** ] 或 [ **DirectQuery** ] 模式來建立儀表板，視案例、規模和效能需求而定。 

## <a name="prerequisites"></a>先決條件

您需要下列專案才能完成這篇文章：

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 屬於 Azure Active Directory 成員的組織電子郵件帳戶，以便您連線到 [Azure 資料總管說明叢集](https://dataexplorer.azure.com/clusters/help/databases/samples)。
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (選取 [免費下載])

## <a name="get-data-from-azure-data-explorer"></a>從 Azure 資料總管取得資料

首先，連線到 Azure 資料總管說明叢集，然後從 *StormEvents* 資料表帶入資料子集。 [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

1. 在 Power BI Desktop **的 [常用**] 索引標籤上，選取 [**取得資料** **]。**

    ![取得資料](media/power-bi-connector/get-data-more.png)

1. 搜尋 *azure 資料總管*，選取 [ **azure 資料總管** 然後連線 **]**。

    ![搜尋並取得資料](media/power-bi-connector/search-get-data.png)

1. 在 **Azure 資料總管 (Kusto)** 畫面上，填寫表單中的下列資訊。

    ![叢集、資料庫、資料表選項](media/power-bi-connector/cluster-database-table.png)

    | 設定 | 值 | 欄位描述
    |---|---|---
    | 叢集 | *https://help.kusto.windows.net* | 說明叢集的 URL。 若是其他叢集，URL 的格式為 HTTPs://。 *\<ClusterName\> \<Region\>kusto.windows.net*。 |
    | 資料庫 | 保留空白 | 裝載於所要連線叢集上的資料庫。 我們會在稍後步驟中選取此項目。 |
    | 資料表名稱 | 保留空白 | 資料庫的其中一個資料表，或是 <code>StormEvents \| take 1000</code>之類的查詢。 我們會在稍後步驟中選取此項目。 |
    | 進階選項 | 保留空白 | 您查詢的選項，例如結果集大小。
    | 資料連線模式 | *DirectQuery* | 決定 Power BI 是否匯入資料或直接連線到資料來源。 您可以使用任一選項搭配此連接器。 |

    > [!NOTE]
    > 在匯 **入** 模式中，資料會移至 Power BI。 在 **DirectQuery** 模式中，資料會直接從您的 Azure 資料總管叢集中查詢。
    >
    > 使用匯 **入** 模式的時機：
    >
    > * 您的資料集很小。
    > * 您不需要近乎即時的資料。
    > * 您的資料已匯總，或您 [在 Kusto 中執行匯總](kusto/query/summarizeoperator.md#list-of-aggregation-functions)
    >
    > 使用 **DirectQuery** 模式的時機：
    > * 您的資料集非常大。
    > * 您需要近乎即時的資料。

    ### <a name="advanced-options"></a>進階選項

    | 設定 | 範例值 | 欄位描述
    |---|---|---
    | 限制查詢結果記錄號碼| `300000` | 要在結果中傳回的記錄數目上限 |
    | 限制查詢結果資料大小 | `4194304` | 要在結果中傳回的資料大小上限（以位元組為單位） |
    | 停用結果集截斷 | `true` | 使用 notruncation 要求選項啟用/停用結果截斷 |
    | 其他 set 語句 | `set query_datascope=hotcache` | 設定查詢持續時間的查詢選項。 查詢選項可控制查詢如何執行和傳回結果。 |

1. 如果您還沒有說明叢集的連線，請登入。 使用組織帳戶登入，然後選取 [連線]。

    ![登入](media/power-bi-connector/sign-in.png)

1. 在 [導覽 **器** ] 畫面上，展開 [ **範例** ] 資料庫，然後選取 [ **StormEvents** 然後 **轉換資料**]。

    ![選取資料表](media/power-bi-connector/select-table.png)

    資料表會在「Power Query 編輯器」中開啟，您可以先在此編輯器中編輯資料列和資料行，然後再匯入資料。

1. 在 Power Query 編輯器中，選取 **DamageCrops** 資料行旁邊的箭號，然後選取 [遞減排序]。

    ![DamageCrops 遞減排序](media/power-bi-connector/sort-descending.png)

1. 在 [首頁] 索引標籤上，選取 [保留資料列]，然後 [保留頂端資料列]。 輸入值 *1000* 可帶入排序資料表的前 1000 個資料列。

    ![保留頂端資料列](media/power-bi-connector/keep-top-rows.png)

1. 在 [首頁] 索引標籤上，選取 [關閉並套用]。

    ![關閉並套用](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>在報告中將資料視覺化

[!INCLUDE [data-explorer-power-bi-visualize-basic](includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>清除資源

如果您不再需要針對本文所建立的報表，請刪除 Power BI Desktop 的 ( .pbix) 檔。

## <a name="next-steps"></a>後續步驟

[使用 Azure 資料總管連接器 Power BI 來查詢資料的秘訣](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)