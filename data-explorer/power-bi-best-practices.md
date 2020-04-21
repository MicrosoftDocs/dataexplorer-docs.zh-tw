---
title: 使用 Power BI 查詢與視覺化 Azure 資料資源管理員資料的最佳做法
description: 在本文中,您將瞭解使用 Power BI 查詢和可視化 Azure 資料資源管理器數據的最佳做法。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 5c5c27afc2c6fa0ee337d336b43222f5ea6583e8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502188"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>使用 Power BI 查詢與視覺化 Azure 資料資源管理員資料的最佳做法

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 [Power BI](https://docs.microsoft.com/power-bi/)是一種業務分析解決方案,可讓您可視化數據並在整個組織中共享結果。 Azure 資料資源管理員提供了三個用於連接到 Power BI 中的數據的選項。 使用[內建連接器](power-bi-connector.md),[將來自 Azure 資料資源管理員的查詢匯入到 Power BI,](power-bi-imported-query.md)或使用[SQL 查詢](power-bi-sql-query.md)。 本文為您提供使用 Power BI 查詢和可視化 Azure 資料資源管理員數據的提示。 

## <a name="best-practices-for-using-power-bi"></a>使用 Power BI 的最佳做法 

使用 TB 的新鮮原始資料時,請遵循以下準則,使 Power BI 儀表板和報表保持快速更新:

* **行駛燈**- 僅將報告所需的數據帶到 Power BI。 對於深度互動式分析,請使用 Azure[資料資源管理器 Web UI,](web-query-data.md)該 UI 針對庫斯托查詢語言進行臨時瀏覽進行了優化。

* **複合模型**- 使用[複合模型](https://docs.microsoft.com/power-bi/desktop-composite-models)將頂級儀錶板的聚合數據與篩選的操作原始數據相結合。 您可以清楚地定義何時使用原始數據以及何時使用聚合視圖。 

* **匯入模式與直接查詢模式**- 使用**匯入**模式對較小的數據集進行交互。 對大型、經常更新的數據集使用**直接查詢**模式。 例如,使用 **「導入」** 模式創建維度表,因為它們很小,並且不會經常更改。 根據預期的數據更新速率設置刷新間隔。 使用**DirectQuery**模式建立事實數據表,因為這些表很大,包含原始數據。 使用這些表使用 Power BI[鑽取](https://docs.microsoft.com/power-bi/desktop-drillthrough)來顯示篩選的數據。

* **並行性**– Azure 資料資源管理員是一個線性擴展的數據平臺,因此,您可以通過提高端到端流的並行性來提高儀表板呈現的性能,如下所示:

   * 增加 Power [BI 中的 DirectQuery 中的併發連接](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)數。

   * 使用[弱一致性來改進並行性](kusto/concepts/queryconsistency.md)。 這可能對數據的新鮮度產生影響。

* **有效的切片器**– 使用[同步切片器](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages)在準備就緒之前防止報表載入數據。 構造資料集、放置所有視覺物件並標記所有切片器后,可以選擇同步切片器以僅載入所需的數據。

* **使用篩選器**- 使用盡可能多的 Power BI 篩選器將 Azure 資料資源管理器搜尋集中到相關數據分片上。

* **高效的視覺物件**– 為資料選擇最性能的視覺效果。

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>使用 Azure 資料資源管理員連接器進行 Power BI 查詢資料的提示

以下部分包括使用 Power BI 使用 Kusto 查詢語言的提示和技巧。 使用[Power BI 的 Azure 資料資源管理員連接器](power-bi-connector.md)視覺化資料

### <a name="complex-queries-in-power-bi"></a>Power BI 中的複雜查詢

與 Power 查詢相比,複雜查詢在 Kusto 中表達更容易。 它們應作為[Kusto 函數](kusto/query/functions/index.md)實現,並在 Power BI 中調用。 在 Kusto 查詢`let`中使用**DirectQuery**與敘述一起使用時,需要此方法。 由於 Power BI 聯`let`接兩個 查詢,並且`join`語句不能與 運算符一起使用,因此可能會出現語法錯誤。 因此,將聯接的每個部分保存為 Kusto 函數,並允許 Power BI 將這兩個函數聯接在一起。

### <a name="how-to-simulate-a-relative-date-time-operator"></a>如何模擬相對的日期和時間運算子

Power BI 不包含*相對*的日期和時間運算子`ago()`,如 。
要類`ago()`比 ,`DateTime.FixedLocalNow()`請`#duration`使用和 Power BI 函數的組合。

而不是使用此運算子的`ago()`查詢:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

使用以下等效查詢:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>達到函式庫斯托查詢限制 

默認情況下,Kusto 查詢返回最多 500,000 行或 64 MB,如[查詢限制](kusto/concepts/querylimits.md)中所述。 可以使用**Azure 資料資源管理員 (Kusto)** 連接視窗中**的進階選項**來覆蓋這些預設值:

![進階選項](media/power-bi-best-practices/advanced-options.png)

這些選項問題將[語句設定為](kusto/query/setstatement.md)查詢以更改預設查詢限制:

  * **限制查詢結果記錄編號**產生`set truncationmaxrecords`
  * **限制查詢結果資料大小(位元組)** 產生`set truncationmaxsize`
  * **關閉結果集截斷**將產生`set notruncation`

### <a name="using-query-parameters"></a>使用查詢參數

可以使用[查詢參數](kusto/query/queryparametersstatement.md)動態修改查詢。 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>在連線詳細資訊中使用查詢參數

使用查詢參數篩選查詢中的資訊並優化查詢性能。
 
編輯**查詢視窗**,**主頁** > **進階編輯器**

1. 尋找查詢的以下部份:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   例如：

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. 將查詢的相關部分替換為參數。 將查詢拆分為多個部分,並使用安培 (&)以及參數將它們串聯回。

   例如,在上面的查詢中,我們將`State == 'ALABAMA'`獲取該部分,並將其拆分為`State == '`:`'``State`並將 參數放在它們之間:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. 如果查詢包含引號,請正確編碼它們。 例如,以下查詢: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   將在**進階編輯器**中顯示,如下所示,並帶有兩個引號:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   應將其替換為以下查詢,並帶有三個引號:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>在查詢步驟中使用查詢參數

您可以在支援查詢的任何查詢步驟中使用查詢參數。 例如,根據參數的值篩選結果。

![使用參數篩選結果](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>不要使用 Power BI 資料刷新計畫程式向 Kusto 發出控制命令

Power BI 包括一個資料刷新計劃程式,該計劃程式可以定期對數據源發出查詢。 此機制不應用於將控制命令安排到 Kusto,因為 Power BI 假定所有查詢都是唯讀的。

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI&lt;只能向 Kusto 傳送簡訊(2000 個字元)查詢

如果在 Power BI 中執行查詢會導致以下錯誤 _:"DataSource.Error:Web.內容無法從..."_ 查詢可能超過 2000 個字元。 Power BI 使用**PowerQuery**透過發出 HTTP GET 請求來查詢 Kusto,該請求將查詢編碼為正在檢索的 URI 的一部分。 因此,Power BI 發出的 Kusto 查詢限制為請求 URI 的最大長度(2000 個字元減去小偏移)。 作為解決方法,您可以在 Kusto 中定義[儲存的函數](kusto/query/schema-entities/stored-functions.md),並在查詢中讓 Power BI 使用該函數。

## <a name="next-steps"></a>後續步驟

[使用適用於 Power BI 的 Azure 資料總管連接器將資料視覺化](power-bi-connector.md)




