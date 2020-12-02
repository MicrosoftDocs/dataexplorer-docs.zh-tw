---
title: Kusto. Explorer 安裝和使用者介面
description: 了解 Kusto.Explorer 的功能，以及其如何協助您探索資料
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 0086fb9f649d7bb3b7031521812c1dff0ca532f7
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513058"
---
# <a name="kustoexplorer-installation-and-user-interface"></a>Kusto. Explorer 安裝和使用者介面

Kusto.Explorer 是一個豐富的桌面應用程式，可讓您在便於使用的使用者介面中，使用 Kusto 查詢語言來探索資料。 本概觀說明如何開始設定您的 Kusto.Explorer，並說明您使用的使用者介面。

使用 Kusto.Explorer，您可以：
* [查詢資料](kusto-explorer-using.md#query-mode)。
* 在資料表之間[搜尋您的資料](kusto-explorer-using.md#search-mode)。
* 在各種不同的圖表中，[將您的資料視覺化](#visualizations-section)。
* 透過電子郵件或使用深層連結[共用查詢和結果](kusto-explorer-using.md#share-queries-and-results)。

## <a name="installing-kustoexplorer"></a>安裝 Kusto.Explorer

* 從下列來源下載並安裝 Kusto.Explorer 工具：
     * [https://aka.ms/ke](https://aka.ms/ke) (CDN 位置)
     * [https://aka.ms/ke-mirror](https://aka.ms/ke-mirror) (非 CDN 位置)

* 請改為在下列位置使用瀏覽器存取您的 Kusto 叢集：`https://<your_cluster>.<region>.kusto.windows.net.`
   以您的 Azure 資料總管叢集名稱和部署區域取代 &lt;your_cluster&gt; 和&lt;區域&gt;。

### <a name="using-chrome-and-kustoexplorer"></a>使用 Chrome 和 Kusto.Explorer

如果您使用 Chrome 作為預設瀏覽器，請務必安裝適用於 Chrome 的 ClickOnce 擴充功能：

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>使用者介面的概觀

Kusto.Explorer 使用者介面的設計是以索引標籤和面板為基礎，類似於其他 Microsoft 產品： 

1. 瀏覽[功能表面版](#menu-panel)上的索引標籤以執行各種作業
2. 在[連線面板](#connections-panel)中管理您的連線
3. 建立指令碼以在指令碼面板中執行
4. 在結果面板中檢視指令碼的結果

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto Explorer 啟動":::

## <a name="menu-panel"></a>功能表面板

Kusto.Explorer [功能表] 面板包含下列索引標籤：

* [首頁](#home-tab)
* [檔案](#file-tab)
* [連線](#connections-tab)
* [檢視](#view-tab)
* [工具](#tools-tab)
* [監視](#monitoring-tab)
* [管理](#management-tab)
* [說明](#help-tab)

### <a name="home-tab"></a>首頁索引標籤

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Kusto Explorer 首頁索引標籤":::

[首頁] 索引標籤會顯示最近使用的功能，分為幾個區段：

* [查詢](#query-section)
* [共用](#share-section)
* [視覺效果](#visualizations-section)
* [檢視](#view-section)
* [說明](#help-tab) 

### <a name="query-section"></a>查詢區段

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="查詢功能表 Kusto Explorer":::

|功能表|    行為|
|----|----------|
|模式下拉式清單 | <ul><li>查詢模式：將 [查詢] 視窗切換為[指令碼模式](kusto-explorer-using.md#query-mode)。 命令可以載入並儲存為指令碼 (預設值)</li> <li> 搜尋模式：單一查詢模式，其中輸入的每個命令都會立即處理，並在 [結果] 視窗中顯示結果</li> <li>搜尋++ 模式：允許在一或多個資料表之間使用搜尋語法搜尋詞彙。 深入了解使用[搜尋++ 模式](kusto-explorer-using.md#search-mode)</li></ul> |
|新增索引標籤| 開啟用來查詢 Kusto 的新索引標籤 |

### <a name="share-section"></a>共用區段

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Kusto Explorer 共用功能表":::

|功能表|    行為|
|----|----------|
|資料至剪貼簿|    將查詢和資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|結果至剪貼簿| 將資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|查詢至剪貼簿| 將查詢匯出至剪貼簿|

### <a name="visualizations-section"></a>視覺效果區段

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Kusto Explorer 功能表視覺效果":::

|功能表         | 行為|
|-------------|---------|
|區域圖      | 顯示一個區域圖，其中 X 軸是第一個資料行 (必須是數值)。 所有數值資料行都會對應到不同的數列 (Y 軸) |
|直條圖 | 顯示所有數值資料行對應至不同數列的直條圖 (Y 軸)。 數值前面的文字資料行是 X 軸 (可在 UI 中控制)|
|長條圖    | 顯示所有數值資料行對應至不同數列的長條圖 (X 軸)。 數值前面的文字資料行是 Y 軸 (可在 UI 中控制)|
|堆疊區域圖      | 顯示一個堆疊區域圖，其中 X 軸是第一個資料行 (必須是數值)。 所有數值資料行都會對應到不同的數列 (Y 軸) |
|時間軸圖表   | 顯示一個時間圖，其中 X 軸是第一個資料行 (必須是日期時間)。 所有數值資料行都會對應到不同的數列 (Y 軸)。|
|折線圖   | 顯示一個折線圖，其中 X 軸是第一個資料行 (必須是數值)。 所有數值資料行都會對應到不同的數列 (Y 軸)。|
|[異常圖表](#anomaly-chart)|    類似於時間圖，但是會使用機器學習異常演算法，尋找時間序列資料中的異常狀況。 對於異常偵測，Kusto.Explorer 會使用 [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) 函式。
|圓形圖    |    顯示圓形圖，其中的色彩軸為第一個資料行。 theta 軸 (必須是量值，轉換成百分比) 是第二個資料行。|
|時間階梯 |    顯示一個階梯圖，其中 X 軸是最後兩個資料行 (必須是日期時間)。 Y 軸是其他資料行的複合。|
|散佈圖| 顯示一個點狀圖表，其中 X 軸是第一個資料行 (必須是數值)。 所有數值資料行都會對應到不同的數列 (Y 軸)。|
|樞紐圖表  | 顯示樞紐分析表和樞紐圖表，提供選取資料、資料行、資料列和各種圖表類型的完整彈性。| 
|時間樞紐分析表   | 在事件時間軸上進行互動式導覽 (在時間軸上進行樞紐分析)|

> [!NOTE]
> <a id="anomaly-chart">異常圖表</a>：演算法預期要有時間序列資料，其中包含兩個資料行：
>* 固定間隔值區中的時間
>* 要在 Kusto.Explorer 中產生時間序列資料的異常偵測數值，以時間欄位進行摘要，並且指定時間值區 bin。

### <a name="view-section"></a>檢視區段

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Kusto Explorer 檢視功能表":::

|功能表           | 行為|
|---------------|---------|
|完整檢視模式 | 藉由隱藏功能區功能表和連線面板，將工作空間最大化。 藉由選取 [首頁]  >  [完整檢視模式] 或按下 **F11**，結束 [完整檢視模式]。|
|隱藏空白資料行| 從資料格移除空白資料行|
|摺疊單數資料行| 摺疊具有單數數值的資料行|
|探索資料行的值| 顯示資料行值分佈|
|加大字型  | 加大查詢索引標籤和結果資料格的字型大小|  
|縮小字型  | 縮小查詢索引標籤和結果資料格的字型大小|

>[!NOTE]
> 資料檢視設定：
>
> Kusto.Explorer 會持續追蹤每一組唯一資料行所使用的設定。 重新排序或移除資料行時，會儲存資料檢視，並在每次取出具有相同資料行的資料時重複使用。 若要將檢視重設為其預設值，請在 [檢視] 索引標籤中，選取 [重設檢視]。 

## <a name="file-tab"></a>[檔案] 索引標籤

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Kusto Explorer 檔案索引標籤":::

|功能表| 行為|
|---------------|---------|
||---------*查詢指令碼*---------|
|新增索引標籤 | 開啟用來查詢 Kusto 的新增索引標籤視窗 |
|開啟檔案| 將 *.kql 檔案中的資料載入至使用中的指令碼面板|
|儲存至檔案| 將使用中指令碼面板中的內容儲存至 *.kql 檔案|
|關閉索引標籤| 關閉目前的索引標籤視窗|
||---------*儲存資料*---------|
|資料至 CSV       | 將資料匯出至 CSV (逗點分隔值) 檔案| 
|資料至 JSON      | 將資料匯出至 JSON 格式的檔案|
|資料至 Excel     | 將資料匯出至 XLSX (Excel) 檔案|
|資料至文字      | 將資料匯出至 TXT (文字) 檔案| 
|資料至 KQL 指令碼| 將查詢匯出至指令檔檔案| 
|資料至結果   | 將查詢和資料匯出至結果 (QRES) 檔案|
|執行查詢至 CSV |執行查詢並且將結果儲存到本機 CSV 檔案|
||---------*載入資料*---------|
|從結果|    從結果 (QRES) 檔案載入查詢和資料| 
||---------*剪貼簿*---------|
|查詢和結果至剪貼簿|    將查詢和資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|結果至剪貼簿| 將資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|查詢至剪貼簿| 將查詢匯出至剪貼簿|
||---------*結果*---------|
|清除結果快取| 清除先前所執行查詢的快取結果| 

## <a name="connections-tab"></a>連線索引標籤

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Kusto Explorer 連線索引標籤":::

|功能表|行為|
|----|----------|
||---------*群組*---------|
|新增群組| 新增 Kusto 伺服器群組|
|重新命名群組| 重新命名現有的 Kusto 伺服器群組|
|移除群組| 移除現有的 Kusto 伺服器群組|
||---------*叢集*---------|
|匯入連線| 從檔案指定連線匯入連線|
|匯出連線| 將連線匯出至檔案|
|加入連接| 新增 Kusto 伺服器連線| 
|編輯連線| 開啟 Kusto 伺服器連線屬性編輯的對話方塊|
|移除連線| 移除與 Kusto 伺服器的現有連線|
|Refresh| 重新整理 Kusto 伺服器連線的屬性|
||---------*安全性*---------|
|檢查您的 AAD 主體| 顯示目前使用中的使用者詳細資料|
|從 AAD 登出| 從與 AAD 的連線登出目前使用者|
||---------*資料範圍*---------|
|快取範圍|<ul><li>只在[經常性存取資料快取](../management/cachepolicy.md)上進行經常性存取 DataExecute 查詢</li><li>所有資料：針對所有可用的資料執行查詢 (預設值)</li></ul> |
|日期時間資料行| 可用於時間預先篩選的資料行名稱|
|時間篩選| 時間預先篩選的值|

## <a name="view-tab"></a>檢視索引標籤

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Kusto Explorer 檢視索引標籤":::

|功能表|行為|
|----|----------|
||---------*外觀*---------|
|完整檢視模式 | 藉由隱藏功能區功能表和連線面板，將工作空間最大化|
|加大字型  | 加大查詢索引標籤和結果資料格的字型大小|  
|縮小字型  | 縮小查詢索引標籤和結果資料格的字型大小|
|重設配置|重設工具停駐控制項和視窗的配置|
|重新命名文件索引標籤 |為選取的索引標籤重新命名 |
||---------*資料檢視*---------|
|重設檢視| 將[資料檢視設定](#dvs)重設為其預設值 |
|探索資料行的值|顯示資料行值分佈|
|專注於查詢統計資料|將焦點變更為查詢統計資料，而不是查詢完成時的查詢結果|
|隱藏複本|切換從查詢結果中移除重複的資料列|
|隱藏空白資料行|切換從查詢結果中移除空白資料行|
|摺疊單數資料行|切換摺疊具有單數數值的資料行|
||---------*資料篩選*---------|
|篩選搜尋中的資料列|切換選項，只在查詢結果搜尋中顯示相符的資料列 (**Ctrl+F**)|
||---------*視覺效果*---------|
|視覺效果|請參閱上方的[視覺效果](#visualizations-section)。 |

> [!NOTE]
> <a id="dvs">資料檢視設定：</a> 
>
> Kusto.Explorer 會持續追蹤每一組唯一資料行所使用的設定。 重新排序或移除資料行時，會儲存資料檢視，並在每次取出具有相同資料行的資料時重複使用。 若要將檢視重設為其預設值，請在 [檢視] 索引標籤中，選取 [重設檢視]。 

## <a name="tools-tab"></a>[Tools] \(工具\) 索引標籤

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Kusto Explorer 工具索引標籤":::

|功能表|行為|
|----|----------|
||---------*IntelliSense*---------|
|啟用 IntelliSense| 啟用和停用指令碼面板上的 IntelliSense|
||---------*分析*---------|
|Query Analyzer| 啟動 Query Analyzer 工具|
|查詢檢查工具 | 分析目前的查詢並輸出一組適用的改進建議|
|計算機| 啟動計算機|
||---------*分析*---------|
|分析報表| 開啟儀表板，其中包含多個預先建置的資料分析報表|
||---------*轉譯*---------|
|查詢 Power BI| 將查詢轉譯成適合在 Power BI 中使用的格式|
||---------*選項*---------|
|重設選項| 將應用程式設定設為預設值|
|選項| 開啟工具以設定應用程式設定。 深入了解 [Kusto.Explorer 選項](kusto-explorer-options.md)。|

## <a name="monitoring-tab"></a>監視索引標籤

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Kusto Explorer 監視索引標籤":::

|功能表             | 行為|
|-----------------|---------| 
||---------*監視*---------|
|叢集診斷 | 顯示目前在 [連線] 面板中所選取伺服器群組的健康情況摘要 | 
|最新的資料：所有資料表| 顯示目前所選取資料庫所有資料表中最新資料的摘要|
|最新的資料：選取的資料表|在狀態列中顯示所選取資料表中的最新資料| 

## <a name="management-tab"></a>管理索引標籤

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto Explorer 管理索引標籤":::

|功能表             | 行為|
|-----------------|---------|
||---------*授權主體*---------|
|管理叢集授權主體 |為授權的使用者啟用管理叢集主體的功能| 
|管理資料庫授權主體 | 為授權的使用者啟用管理資料庫主體的功能| 
|管理資料表授權主體 | 為授權的使用者啟用管理資料表主體的功能| 
|管理函式授權主體 | 為授權的使用者啟用管理函式主體的功能| 

## <a name="help-tab"></a>說明索引標籤

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Kusto Explorer 說明索引標籤":::

|功能表             | 行為|
|-----------------|---------|
||---------*文件*---------|
|説明             | 開啟 Kusto 線上文件的連結  | 
|新功能       | 開啟文件，其中列出所有 Kusto.Explorer 的變更|
|報告問題      |開啟包含兩個選項的對話方塊： <ul><li>報告與服務相關的問題</li><li>報告用戶端應用程式中的問題</li></ul> | 
|建議功能  | 開啟 Kusto 意見反應論壇的連結 | 
|檢查更新     | 檢查您的 Kusto.Explorer 版本是否有更新 | 

## <a name="connections-panel"></a>連線面板

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Kusto Explorer 連線面板":::

[連線] 窗格會顯示所有已設定的叢集連線。 針對每個叢集，會顯示其儲存的資料庫、資料表和屬性 (資料行)。 選取項目 (在主面板中設定搜尋/查詢的隱含內容)，或按兩下項目以將名稱複製到搜尋/查詢面板。

如果實際的結構描述很大 (例如具有數百個資料表的資料庫)，您可以按下 **CTRL+F** 進行搜尋，然後輸入您所尋找實體名稱的子字串 (不區分大小寫)。

Kusto.Explorer 支援從查詢視窗控制 [連線] 面板，這對指令碼很有用。 例如，您可以使用下列語法透過命令來啟動指令碼檔案，該命令會指示 Kusto.Explorer 連線到由指令碼查詢其資料的叢集/資料庫：

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

使用 `F5` 或類似項目對每一行執行。

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>控制連線到 Kusto.Explorer 的使用者身分識別

新連線的預設安全性模型是 AAD 同盟安全性。 驗證是透過 Azure Active Directory 使用預設 AAD 使用者體驗來完成。

如果您需要更精細地控制驗證參數，您可以展開 [進階：連線字串] 編輯方塊，並且提供有效的 [Kusto 連接字串](../api/connection-strings/kusto.md)值。

例如，存在於多個 AAD 租用戶中的使用者，有時需要將其身分識別的特定「投影」用於特定 AAD 租用戶。 若要這麼做，請提供連接字串，例如以下的範例 (以特定值取代大寫的單字)：

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER` 是叢集裝載所在 AAD 租用戶的網域名稱或 AAD 租用戶識別碼 (GUID)。 這通常是擁有叢集組織的網域名稱，例如 `contoso.com`。 
* USER_DOMAIN 是受邀加入該租用戶的使用者身分識別 (例如，`user@example.com`)。 

>[!NOTE]
> 使用者的網域名稱不一定與主控叢集的租用戶名稱相同。

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Kusto Explorer 進階連接字串":::

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

您可能會發現使用鍵盤快速鍵可讓您比使用滑鼠更快執行作業。 若要深入了解，請參閱這個 [Kusto.Explorer 鍵盤快速鍵的清單](kusto-explorer-shortcuts.md)。

## <a name="table-row-colors"></a>資料表資料列色彩

Kusto.Explorer 會嘗試解譯結果面板中每個資料列的嚴重性和詳細程度層級，並且據以調整其色彩。 其方式是將每個資料行的相異值與一組已知模式 (「警告」、「錯誤」等等) 進行比對。

若要修改輸出色彩配置，或關閉此行為，請從 [工具] 功能表中選取 [選項]  >  [結果檢視器]  >  [詳細程度色彩配置]。

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Kusto Explorer 色彩配置修改":::

## <a name="next-steps"></a>後續步驟

深入了解如何使用 Kusto.Explorer：

* [使用 Kusto.Explorer](kusto-explorer-using.md)
* [Kusto.Explorer 鍵盤快速鍵](kusto-explorer-shortcuts.md)
* [Kusto.Explorer 選項](kusto-explorer-options.md)
* [針對 Kusto 進行疑難排解](kusto-explorer-troubleshooting.md)

深入了解 Kusto.Explorer 工具和公用程式：
* [Kusto.Explorer 程式碼分析器](kusto-explorer-code-analyzer.md)
* [Kusto.Explorer 程式碼導覽](kusto-explorer-codenav.md)
* [Kusto.Explorer 程式碼重構](kusto-explorer-refactor.md)
* [Kusto 查詢語言 (KQL)](/azure/kusto/query/)