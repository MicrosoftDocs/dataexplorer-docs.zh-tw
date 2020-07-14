---
title: Kusto 入門
description: 瞭解 Kusto 的功能，以及它如何協助您流覽資料
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: 1f3b273260451dc0ce36730d20f1bc357a453397
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280538"
---
# <a name="getting-started-with-kustoexplorer"></a>Kusto 入門

Kusto 是一個豐富的桌面應用程式，可讓您在便於使用的使用者介面中，使用 Kusto 查詢語言來流覽資料。 本總覽說明如何開始設定您的 Kusto，並說明您將使用的使用者介面。

透過 Kusto，您可以：
* [查詢您的資料](kusto-explorer-using.md#query-mode)。
* 跨資料表[搜尋您的資料](kusto-explorer-using.md#search-mode)。
* 將各種圖形中的[資料視覺化](#visualizations-section)。
* 透過電子郵件或使用深層連結[，共用查詢和結果](kusto-explorer-using.md#share-queries-and-results)。

## <a name="installing-kustoexplorer"></a>安裝 Kusto

* 安裝[Kusto 工具](https://aka.ms/ke)。

* 相反地，請在下列位置使用瀏覽器存取您的 Kusto 叢集：`https://<your_cluster>.kusto.windows.net.`
   &lt; &gt; 以您的 Azure 資料總管叢集名稱取代 your_cluster。

### <a name="using-chrome-and-kustoexplorer"></a>使用 Chrome 和 Kusto

如果您使用 Chrome 作為預設瀏覽器，請務必安裝適用于 Chrome 的 ClickOnce 擴充功能：

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>使用者介面的總覽

Kusto. Explorer 使用者介面的設計是以索引標籤和麵板為基礎，類似于其他 Microsoft 產品的版面配置： 

1. 流覽 [[功能表] 面板](#menu-panel)上的索引標籤，以執行各種作業
2. 在 [連線][面板](#connections-panel)中管理您的連線
3. 建立腳本以在腳本面板中執行
4. 在 [結果] 面板中，查看腳本的結果

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto Explorer 啟動":::

## <a name="menu-panel"></a>功能表面板

[Kusto] 功能表面板包含下列索引標籤：

* [首頁](#home-tab)
* [檔案](#file-tab)
* [連線](#connections-tab)
* [檢視](#view-tab)
* [工具](#tools-tab)
* [監視](#monitoring-tab)
* [管理](#management-tab)
* [說明](#help-tab)

### <a name="home-tab"></a>首頁索引標籤

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Kusto Explorer [首頁] 索引標籤":::

[首頁] 索引標籤會顯示最近使用的函式，分為幾個區段：

* [查詢](#query-section)
* [共用](#share-section)
* [視覺效果](#visualizations-section)
* [檢視](#view-section)
* [說明](#help-tab) 

### <a name="query-section"></a>查詢區段

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="查詢功能表 Kusto Explorer":::

|功能表|    行為|
|----|----------|
|模式下拉式清單 | <ul><li>查詢模式：將查詢視窗切換至[腳本模式](kusto-explorer-using.md#query-mode)。 命令可以載入並儲存為腳本 (預設值) </li> <li> 搜尋模式：單一查詢模式，其中輸入的每個命令都會立即處理，並在結果視窗中顯示結果</li> <li>搜尋 + + 模式：允許在一或多個資料表中使用搜尋語法搜尋詞彙。 深入瞭解如何使用[搜尋 + + 模式](kusto-explorer-using.md#search-mode)</li></ul> |
|新增索引標籤| 開啟用來查詢 Kusto 的新索引標籤 |

### <a name="share-section"></a>Share 區段

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Kusto Explorer 共用功能表":::

|功能表|    行為|
|----|----------|
|資料到剪貼簿|    將查詢和資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|結果至剪貼簿| 將資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|查詢至剪貼簿| 將查詢匯出至剪貼簿|

### <a name="visualizations-section"></a>視覺效果區段

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Kusto Explorer 功能表視覺效果":::

|功能表         | 行為|
|-------------|---------|
|區域圖      | 顯示一個區域圖，其中 X 軸是第一個資料行 (必須是數值) 。 所有數值資料行都會對應到不同的數列 (Y 軸)  |
|直條圖 | 顯示將所有數值資料行對應至不同數列 (Y 軸) 的直條圖。 數值前面的文字資料行是可在 UI 中控制的 X 軸 () |
|長條圖    | 顯示橫條圖，其中所有數值資料行都對應到不同數列 (X 軸) 。 數值前面的文字資料行是可以在 UI 中控制的 Y 軸 () |
|堆疊區域圖      | 顯示堆疊區域圖，其中 X 軸是 (必須是數值) 的第一個資料行。 所有數值資料行都會對應到不同的數列 (Y 軸)  |
|時間軸圖表   | 顯示時間圖表，其中 X 軸是第一個資料行 (必須是日期時間) 。 所有數值資料行都會對應到不同的數列， (Y 軸) 。|
|折線圖   | 顯示折線圖，其中 X 軸是 (必須是數值) 的第一個資料行。 所有數值資料行都會對應到不同的數列， (Y 軸) 。|
|[異常圖表](#anomaly-chart)|    類似于 timechart，但會使用機器學習異常演算法，尋找時間序列資料中的異常狀況。 對於異常偵測，Kusto 會使用[series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md)函數。
|圓形圖    |    顯示圖形，其中的彩色軸為第一個資料行。 Theta 軸 (必須是量值，轉換成百分比) 是第二個數據行。|
|時間階梯 |    顯示一個階梯圖表，其中 X 軸是最後兩個數據行 (必須是日期時間) 。 Y 軸是其他資料行的複合。|
|散佈圖| 顯示點圖，其中 X 軸是第一個資料行 (必須是數值) 。 所有數值資料行都會對應到不同的數列， (Y 軸) 。|
|樞紐圖表  | 顯示樞紐分析表和樞紐分析表，提供選取資料、資料行、資料列和各種圖表類型的完整彈性。| 
|時間樞紐分析表   | 在事件的時間軸上進行互動式導覽 (在時間軸上旋轉) |

> [!NOTE]
> <a id="anomaly-chart">異常圖表</a>：演算法需要時間序列資料，其中包含兩個數據行：
>* 固定間隔值區中的時間
>* 要在 Kusto 中產生時間序列資料的異常偵測的數值，以 [時間] 欄位摘要，並指定 [時間段] bin。

### <a name="view-section"></a>View 區段

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Kusto Explorer 視圖功能表":::

|功能表           | 行為|
|---------------|---------|
|完整視圖模式 | 藉由隱藏功能區功能表和連接面板，將工作空間最大化。 選取 [**首頁**  >  **] [全視圖模式]**，或按**F11**鍵結束 [完整視圖] 模式。|
|隱藏空白資料行| 從資料格移除空的資料行|
|折迭單數資料行| 折迭具有單數值的資料行|
|探索資料行值| 顯示資料行值分佈|
|增加字型  | 增加 [查詢] 索引標籤和 [結果] 資料格的字型大小|  
|減少字型  | 縮小 [查詢] 索引標籤和 [結果] 資料格的字型大小|

>[!NOTE]
> 資料檢視設定：
>
> Kusto 會持續追蹤每一組唯一資料行所使用的設定。 重新排列或移除資料行時，會儲存資料檢視，並在每次抓取具有相同資料行的資料時重複使用。 若要將視圖重設為預設值，請在 [**視圖**] 索引標籤中選取 [**重設視圖**]。 

## <a name="file-tab"></a>[檔案] 索引標籤

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Kusto Explorer [檔案] 索引標籤":::

|功能表| 行為|
|---------------|---------|
||---------*查詢腳本*---------|
|新增索引標籤 | 開啟新的索引標籤視窗以查詢 Kusto |
|開啟檔案| 將 kql 檔案中的資料載入至作用中的腳本面板|
|儲存至檔案| 將 active script 面板的內容儲存至 *. kql 檔案|
|關閉索引標籤| 關閉目前的索引標籤視窗|
||---------*儲存資料*---------|
|資料到 CSV       | 將資料匯出至 CSV (逗號分隔值) 檔案| 
|資料到 JSON      | 將資料匯出至 JSON 格式的檔案|
|資料至 Excel     | 將資料匯出至 .XLSX (Excel) 檔案|
|資料到文字      | 將資料匯出至 TXT (文字) 檔| 
|要 KQL 的資料腳本| 將查詢匯出至腳本檔案| 
|資料到結果   |  (QRES) 檔案中，將查詢和資料匯出至結果|
|在 CSV 中執行查詢 |執行查詢，並將結果儲存到本機 CSV 檔案|
||---------*載入資料*---------|
|從結果|    從結果載入查詢和資料 (QRES) 檔| 
||---------*粘貼*---------|
|查詢和結果至剪貼簿|    將查詢和資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|結果至剪貼簿| 將資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|查詢至剪貼簿| 將查詢匯出至剪貼簿|
||---------*更*---------|
|清除結果快取| 清除先前執行之查詢的快取結果| 

## <a name="connections-tab"></a>連線索引標籤

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Kusto Explorer [連接] 索引標籤":::

|功能表|行為|
|----|----------|
||---------*部門*---------|
|新增群組| 新增 Kusto 伺服器群組|
|重新命名群組| 重新命名現有的 Kusto 伺服器群組|
|移除群組| 移除現有的 Kusto 伺服器群組|
||---------*叢集*---------|
|匯入連接| 從指定連接的檔案匯入連接|
|匯出連接| 匯出檔案的連接|
|加入連接| 新增 Kusto 伺服器連接| 
|編輯連接| 開啟 Kusto 伺服器連接屬性編輯的對話方塊|
|移除連接| 移除 Kusto 伺服器的現有連線|
|重新整理| 重新整理 Kusto 伺服器連接的屬性|
||---------*安全級*---------|
|檢查您的新增主體| 顯示電流作用中使用者詳細資料|
|從 AAD 登出| 登出目前使用者與 AAD 的連線|
||---------*資料範圍*---------|
|快取範圍|<ul><li>僅限[熱資料](../management/cachepolicy.md)快取的熱 DataExecute 查詢</li><li>所有資料：針對所有可用的資料執行查詢 (預設值) </li></ul> |
|日期時間資料行| 可能用於時間預先篩選的資料行名稱|
|時間篩選| 時間預先篩選的值|

## <a name="view-tab"></a>檢視索引標籤

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Kusto Explorer 視圖索引標籤":::

|功能表|行為|
|----|----------|
||---------*效果*---------|
|完整視圖模式 | 藉由隱藏功能區功能表和連接面板，將工作空間最大化|
|增加字型  | 增加 [查詢] 索引標籤和 [結果] 資料格的字型大小|  
|減少字型  | 縮小 [查詢] 索引標籤和 [結果] 資料格的字型大小|
|重設版面配置|重設工具的停駐控制項和視窗的版面配置|
|重新命名檔索引標籤 |重新命名選取的索引標籤 |
||---------*資料檢視*---------|
|重設檢視| 將[資料檢視設定](#dvs)重設為預設值 |
|探索資料行值|顯示資料行值分佈|
|專注于查詢統計資料|將焦點變更為查詢統計資料，而不是查詢完成時的查詢結果|
|隱藏重複專案|切換從查詢結果中移除重複的資料列|
|隱藏空白資料行|切換移除查詢結果中的空白資料行|
|折迭單數資料行|切換折迭具有單數值的資料行|
||---------*資料篩選*---------|
|篩選搜尋中的資料列|切換選項，使其只在查詢結果搜尋中顯示相符的資料列 (**Ctrl + F**) |
||---------*項*---------|
|視覺效果|請參閱上方的[視覺效果](#visualizations-section)。 |

> [!NOTE]
> <a id="dvs">資料檢視設定：</a> 
>
> Kusto 會持續追蹤每一組唯一資料行所使用的設定。 重新排列或移除資料行時，會儲存資料檢視，並在每次抓取具有相同資料行的資料時重複使用。 若要將視圖重設為預設值，請在 [**視圖**] 索引標籤中選取 [**重設視圖**]。 

## <a name="tools-tab"></a>[Tools] \(工具\) 索引標籤

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Kusto Explorer [工具] 索引標籤":::

|功能表|行為|
|----|----------|
||---------*感知*---------|
|啟用 IntelliSense| 啟用和停用腳本面板上的 IntelliSense|
||---------*分析*---------|
|Query Analyzer| 啟動 Query Analyzer 工具|
|查詢檢查程式 | 分析目前的查詢並輸出一組適用的改進建議|
|計算機| 啟動計算機|
||---------*分析*---------|
|分析報表| 開啟儀表板，其中包含多個預先建立的資料分析報表|
||---------*翻譯*---------|
|查詢 Power BI| 將查詢轉譯成適合在 Power BI 中使用的格式|
||---------*選項*---------|
|重設選項| 將應用程式設定設為預設值|
|選項| 開啟用來進行應用程式設定的工具。 深入瞭解[Kusto 選項](kusto-explorer-options.md)。|

## <a name="monitoring-tab"></a>[監視] 索引標籤

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Kusto Explorer [監視] 索引標籤":::

|功能表             | 行為|
|-----------------|---------| 
||---------*監視*---------|
|叢集診斷 | 顯示目前在 [連線] 面板中選取之伺服器群組的健全狀況摘要 | 
|最新資料：所有資料表| 顯示目前所選資料庫的所有資料表中最新資料的摘要|
|最新資料：選取的資料表|在狀態列中顯示所選取資料表中的最新資料| 

## <a name="management-tab"></a>[管理] 索引標籤

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto Explorer [管理] 索引標籤":::

|功能表             | 行為|
|-----------------|---------|
||---------*授權主體*---------|
|管理叢集授權的主體 |為授權的使用者啟用管理叢集主體的功能| 
|管理資料庫授權的主體 | 針對已授權的使用者，啟用管理資料庫的主體| 
|管理已授權的資料表主體 | 為授權的使用者啟用管理資料表的主體| 
|管理已授權的函式主體 | 針對已授權的使用者，啟用管理函式主體的功能| 

## <a name="help-tab"></a>[說明] 索引標籤

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Kusto Explorer [說明] 索引標籤":::

|功能表             | 行為|
|-----------------|---------|
||---------*附帶*---------|
|說明             | 開啟 Kusto 線上檔的連結  | 
|新功能       | 開啟一份檔，列出所有 Kusto 的變更|
|報告問題      |開啟包含兩個選項的對話方塊： <ul><li>報告與服務相關的問題</li><li>報告用戶端應用程式中的問題</li></ul> | 
|建議功能  | 開啟 Kusto 意見反應論壇的連結 | 
|檢查更新     | 檢查您的 Kusto 版本是否有更新。 | 

## <a name="connections-panel"></a>連接面板

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Kusto Explorer 連接面板":::

[連線] 窗格會顯示所有已設定的叢集連接。 針對每個叢集，會顯示資料庫、資料表和屬性 (資料行所儲存的) 。 選取 [專案] (在主要面板) 中設定搜尋/查詢的隱含內容，或按兩下 [專案] 將名稱複製到 [搜尋/查詢] 面板。

如果實際的架構是大型的 (例如具有數百個數據表) 的資料庫，您可以按下**CTRL + F**並輸入子字串 (您要尋找之機構名稱的不區分大小寫) 來進行搜尋。

Kusto 支援從查詢視窗控制連接面板，這對腳本很有用。 例如，您可以使用下列語法來啟動腳本檔案，其命令會指示 Kusto 連接到由腳本查詢其資料的叢集/資料庫：

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

使用或類似的來執行每一行 `F5` 。

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>控制連接到 Kusto 的使用者身分識別

新連接的預設安全性模型是 AAD 同盟安全性。 驗證是透過 Azure Active Directory 使用預設 AAD 使用者體驗來完成。

如果您需要更精細地控制驗證參數，您可以展開 [高級：連接字串] 編輯方塊，並提供有效的[Kusto 連接字串](../api/connection-strings/kusto.md)值。

例如，存在於多個 AAD 租使用者中的使用者，有時需要將其身分識別的特定「投射」用於特定 AAD 租使用者。 若要這麼做，請提供連接字串，如下所示 (將大寫的單字取代為特定值) ：

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER`是功能變數名稱或 AAD 租使用者識別碼， (裝載叢集之 AAD 租使用者的 GUID) 。 這通常是擁有叢集之組織的功能變數名稱，例如 `contoso.com` 。 
* USER_DOMAIN 是受邀加入該租使用者的使用者身分識別 (例如 `user@example.com`) 。 

>[!Note]
> 使用者的功能變數名稱不一定與主控叢集的租使用者名稱相同。

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Kusto Explorer 的 advanced 連接字串":::

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

您可能會發現使用鍵盤快速鍵可讓您比使用滑鼠更快執行作業。 如需深入瞭解，請參閱這[份 Kusto 的鍵盤快速鍵清單](kusto-explorer-shortcuts.md)。

## <a name="table-row-colors"></a>資料表資料列色彩

Kusto 會嘗試解讀 [結果] 面板中每個資料列的嚴重性或詳細等級，並據以調整其色彩。 其執行方式是將每個資料行的相異值與一組已知模式（ ( "Warning"、"Error" 等）進行比對，依此類推) 。

若要修改輸出色彩配置，或關閉此行為，請從 [**工具**] 功能表選取 [**選項**  >  **] [結果檢視器**  >  **詳細資訊色彩配置**]。

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Kusto Explorer 色彩配置修改":::

## <a name="next-steps"></a>後續步驟

深入瞭解如何使用 Kusto：

* [使用 Kusto.Explorer](kusto-explorer-using.md)
* [Kusto. Explorer 鍵盤快速鍵](kusto-explorer-shortcuts.md)
* [Kusto.Explorer 選項](kusto-explorer-options.md)
* [針對 Kusto 進行疑難排解](kusto-explorer-troubleshooting.md)

深入瞭解 Kusto. Explorer 工具和公用程式：
* [Kusto. Explorer 程式碼分析器](kusto-explorer-code-analyzer.md)
* [Kusto. Explorer 程式碼導覽](kusto-explorer-codenav.md)
* [Kusto 程式碼重構](kusto-explorer-refactor.md)
* [Kusto 查詢語言 (KQL)](https://docs.microsoft.com/azure/kusto/query/)
