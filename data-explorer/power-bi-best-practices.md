---
title: 使用 Power BI 來查詢 Azure 資料總管資料並將其視覺化的最佳作法
description: 在本文中，您將瞭解使用 Power BI 來查詢 Azure 資料總管資料並將其視覺化的最佳作法。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/26/2019
ms.openlocfilehash: 404d8f2d6b7eacc61571575613fd8017baadb54d
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614844"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>使用 Power BI 來查詢 Azure 資料總管資料並將其視覺化的最佳作法

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 [Power BI](https://docs.microsoft.com/power-bi/) 是一種商務分析解決方案，可讓您將資料視覺化，並在整個組織中共用結果。 Azure 資料總管提供三種連接到 Power BI 中資料的選項。 使用 [內建連接器](power-bi-connector.md)， [從 Azure 資料總管將查詢匯入 Power BI](power-bi-imported-query.md)，或使用 [SQL 查詢](power-bi-sql-query.md)。 本文提供您使用 Power BI 查詢和視覺化 Azure 資料總管資料的秘訣。 

## <a name="best-practices-for-using-power-bi"></a>使用 Power BI 的最佳作法

使用數 tb 的最新原始資料時，請遵循下列指導方針，讓 Power BI 的儀表板和報表保持 snappy 和更新：

* **旅遊燈** -只將報表所需的資料帶入 Power BI。 如需深入的互動式分析，請使用 [Azure 資料總管 WEB UI](web-query-data.md) ，該 UI 已針對使用 Kusto 查詢語言進行臨機操作探索進行優化。

* **複合模型** -使用 [複合模型](https://docs.microsoft.com/power-bi/desktop-composite-models) ，將最上層儀表板的匯總資料與篩選的操作原始資料合併。 您可以清楚地定義何時使用原始資料，以及何時使用匯總視圖。 

* 匯**入模式與 DirectQuery 模式**-使用匯**入**模式來進行較小的資料集互動。 針對大型、經常更新的資料集使用 **DirectQuery** 模式。 例如，使用 [匯 **入** ] 模式建立維度資料表，因為它們很小，而且不常變更。 根據預期的資料更新速率來設定重新整理間隔。 使用 **DirectQuery** 模式建立事實資料表，因為這些資料表很大且包含原始資料。 使用這些資料表，利用 Power BI 的 [鑽取](https://docs.microsoft.com/power-bi/desktop-drillthrough)來呈現篩選過的資料。

* **平行** 處理原則-Azure 資料總管是可線性調整的資料平臺，因此，您可以藉由增加端對端流程的平行處理原則來改善儀表板轉譯的效能，如下所示：

  * 在 [Power BI 中增加 DirectQuery 的並行連接](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)數目。

  * 使用 [弱式一致性來改善平行](kusto/concepts/queryconsistency.md)處理原則。 這可能會影響資料的時效性。

* **有效** 交叉分析篩選器–使用 [同步](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) 交叉分析篩選器，在您準備就緒之前，防止報表載入資料。 在您結構資料集、放置所有視覺效果，並標示所有交叉分析篩選器之後，您可以選取同步交叉分析篩選器，只載入所需的資料。

* **使用篩選器** -盡可能使用最多的 Power BI 篩選，以專注于 Azure 資料總管搜尋相關的資料分區。

* **有效率的視覺效果** -為您的資料選取效能最高的視覺效果。


## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>使用 Azure 資料總管連接器 Power BI 來查詢資料的秘訣

下一節包含搭配 Power BI 使用 Kusto 查詢語言的秘訣和訣竅。 使用 [適用于 Power BI 的 Azure 資料總管連接器](power-bi-connector.md) 將資料視覺化

### <a name="complex-queries-in-power-bi"></a>Power BI 中的複雜查詢

在 Kusto 中，複雜的查詢比 Power Query 更容易表示。 這些函數應該實作為 [Kusto](kusto/query/functions/index.md)函式，並在 Power BI 中叫用。 在 Kusto 查詢中使用 **DirectQuery** 搭配語句時，需要這個方法 `let` 。 因為 Power BI 聯結兩個查詢，而 `let` 語句不能與運算子一起使用 `join` ，所以可能會發生語法錯誤。 因此，請將聯結的每個部分儲存為 Kusto 函數，並允許 Power BI 將這兩個函式聯結在一起。

### <a name="how-to-simulate-a-relative-date-time-operator"></a>如何模擬相對的日期時間運算子

Power BI 不包含 *相對* 的日期時間運算子，例如 `ago()` 。
若要模擬 `ago()` ，請使用 `DateTime.FixedLocalNow()` 和 `#duration` Power BI 函數的組合。

而不是使用運算子來 `ago()` 執行此查詢：

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

使用下列對等查詢：

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="configuring-azure-data-explorer-connector-options-in-m-query"></a>在 M 查詢中設定 Azure 資料總管 connector 選項

您可以從 PBI 的進階編輯器中，以 M 查詢語言設定 Azure 資料總管連接器的選項。 您可以使用這些選項來控制要傳送至 Azure 資料總管叢集的已產生查詢。

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", [<options>])
in
    Source
```

您可以使用 M 查詢中的下列任何選項：

| 選項 | 範例 | 描述
|---|---|---
| MaxRows | `[MaxRows=300000]` | 將 `truncationmaxrecords` set 語句加入至查詢。 覆寫查詢可能傳回給呼叫者的預設最大記錄數目 (截斷) 。
| MaxSize | `[MaxSize=4194304]` | 將 `truncationmaxsize` set 語句加入至查詢。 覆寫預設的資料大小上限：查詢可以傳回給呼叫者 (截斷) 。
| NoTruncate | `[NoTruncate=true]` | 將 `notruncation` set 語句加入至查詢。 允許隱藏傳回給呼叫端的查詢結果截斷。
| AdditionalSetStatements | `[AdditionalSetStatements="set query_datascope=hotcache"]` | 將提供的 set 語句加入至查詢。 這些語句是用來設定查詢持續時間的查詢選項。 查詢選項可控制查詢如何執行和傳回結果。
| CaseInsensitive | `[CaseInsensitive=true]` | 讓連接器產生不區分大小寫的查詢- `=~` 在比較值時，查詢會使用運算子而不是 `==` 運算子。

    > [!NOTE]
    > You can combine multiple options together to reach the desired behavior: `[NoTruncate=true, CaseInsensitive=true]`

### <a name="reaching-kusto-query-limits"></a>到達 Kusto 查詢限制

依預設，Kusto 查詢會傳回最多500000個數據列或 64 MB，如 [查詢限制](kusto/concepts/querylimits.md)中所述。 您可以使用**Azure 資料總管 (Kusto) **連線視窗中的 [ **Advanced options** ] 來覆寫這些預設值：

![進階選項](media/power-bi-best-practices/advanced-options.png)

這些選項會與您的查詢一起發出 [set 語句](kusto/query/setstatement.md) ，以變更預設的查詢限制：

* **限制查詢結果記錄號碼** 會產生 `set truncationmaxrecords`
* **限制查詢結果資料大小（以位元組為單位）** 產生 `set truncationmaxsize`
* **停用結果集截斷** 會產生 `set notruncation`

### <a name="case-sensitivity"></a>區分大小寫

根據預設，連接器會 `==` 在比較字串值時產生使用區分大小寫運算子的查詢。 如果資料不區分大小寫，這就不是想要的行為。 若要變更產生的查詢，請使用 `CaseInsensitive` connector 選項：

```m
let
    Source = AzureDataExplorer.Contents("help", "Samples", "StormEvents", [CaseInsensitive=true]),
    #"Filtered Rows" = Table.SelectRows(Source, each [State] == "aLaBama")
in
    #"Filtered Rows"
```

### <a name="using-query-parameters"></a>使用查詢參數

您可以使用 [查詢參數](kusto/query/queryparametersstatement.md) 動態修改查詢。 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>在連接詳細資料中使用查詢參數

使用查詢參數來篩選查詢中的資訊並優化查詢效能。
 
在 [**編輯查詢**] 視窗中， **Home**  >  **進階編輯器**

1. 尋找查詢的下一節：

    ```m
    Source = AzureDataExplorer.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```

   例如：

    ```m
    Source = AzureDataExplorer.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. 以您的參數取代查詢的相關部分。 將查詢分割成多個部分，並使用連字號 ( # A0) 和參數將它們串連在一起。

   例如，在上述查詢中，我們將會取得 `State == 'ALABAMA'` 部分，並將它分割為： `State == '` 和， `'` 我們會將參數放置在 `State` 它們之間：

    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. 如果您的查詢包含引號，請正確編碼。 例如，下列查詢：

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100"
   ```

   會以下列兩個引號出現在 **進階編輯器** 中：

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   應以下列查詢取代為三個引號：

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>在查詢步驟中使用查詢參數

您可以在任何支援的查詢步驟中使用查詢參數。 例如，根據參數的值篩選結果。

![使用參數篩選結果](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>請勿使用 Power BI 資料重新整理排程器將控制命令發出至 Kusto

Power BI 包含可定期針對資料來源發出查詢的資料重新整理排程器。 這種機制不應該用來排程 Kusto 的控制命令，因為 Power BI 假設所有的查詢都是唯讀的。

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI 只能將短 (&lt; 2000 字元) 查詢傳送至 Kusto

如果在 Power BI 中執行查詢，會產生下列錯誤：「 _DataSource. error： Web. 內容無法取得內容 ..._ 」查詢的長度可能超過2000個字元。 Power BI 使用 **PowerQuery** 來查詢 Kusto，方法是發出 HTTP GET 要求，以將查詢編碼為要抓取之 URI 的一部分。 因此，Power BI 所發出的 Kusto 查詢限制為要求 URI 的最大長度 (2000 個字元，減去小位移) 。 因應措施是，您可以在 Kusto 中定義 [預存函數](kusto/query/schema-entities/stored-functions.md) ，並讓 Power BI 在查詢中使用該函數。

## <a name="next-steps"></a>後續步驟

[使用適用於 Power BI 的 Azure 資料總管連接器將資料視覺化](power-bi-connector.md)
