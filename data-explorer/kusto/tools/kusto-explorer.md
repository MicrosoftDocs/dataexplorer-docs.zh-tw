---
title: Kusto. Explorer 工具-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto 工具。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 1a643a282deec5a98230a17e7335fff7638812b8
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108434"
---
# <a name="kustoexplorer-tool"></a>Kusto. Explorer 工具

Kusto 是一種豐富的桌面應用程式，可讓您使用 Kusto 查詢語言來流覽您的資料。

## <a name="getting-the-tool"></a>取得工具

* 安裝[Kusto. Explorer 工具](https://aka.ms/Kusto.Explorer)

* 或者，使用瀏覽器存取您的 Kusto 叢集， `https://<your_cluster>.kusto.windows.net`網址為：。 以您的 Azure 資料總管叢集名稱取代 <your_cluster>。



## <a name="using-chrome-and-kustoexplorer"></a>使用 Chrome 和 Kusto

如果您使用 Chrome 作為預設瀏覽器，請務必安裝適用于 Chrome 的 ClickOnce 擴充功能：

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)



## <a name="overview-of-the-user-experience"></a>使用者體驗總覽

Kusto Explorer 視窗有幾個 UI 元件：

1. [功能表面板](#menu-panel)
2. [連接面板](#connections-panel)
3. 腳本面板
4. 結果面板

![Kusto. Explorer 啟動](./Images/KustoTools-KustoExplorer/ke-start.png "ke-啟動")

### <a name="keyboard-shortcuts"></a>鍵盤快速鍵

您可能會發現使用鍵盤快速鍵可讓您比使用滑鼠更快執行作業。 請看一下這份[Kusto 的鍵盤快速鍵清單](kusto-explorer-shortcuts.md)。

### <a name="menu-panel"></a>功能表面板

[Kusto] 功能表面板包含下列索引標籤：

* [首頁](#home-tab)
* [檔案](#file-tab)
* [連線](#connections-tab)
* [檢視](#view-tab)
* [工具](#tools-tab)

* [管理](#management-tab)
* [說明](#help-tab)

### <a name="home-tab"></a>首頁索引標籤

![Kusto. Explorer 首頁](./Images/KustoTools-KustoExplorer/home-tab.png "home-tab")

[首頁] 索引標籤會顯示最近使用的函式，分為幾個區段：

* [查詢](#query-section)
* [共用](#share-section)
* [視覺效果](#visualizations-section)
* [檢視](#view-section)
* [說明](#help-tab) 

#### <a name="query-section"></a>查詢區段

![Kusto. Explorer 查詢功能表](./Images/KustoTools-KustoExplorer/home-query-menu.png "查詢-功能表")

|功能表|    行為|
|----|----------|
|模式下拉式清單 | <ul><li>查詢模式：將查詢視窗切換至[腳本模式](#query-mode)。 命令可以載入並儲存為腳本（預設值）</li> <li> 搜尋模式：單一查詢模式，其中輸入的每個命令都會立即處理，並在結果視窗中顯示結果</li> <li>搜尋 + + 模式：允許在一或多個資料表中使用搜尋語法搜尋詞彙。 深入瞭解如何使用[搜尋 + + 模式](kusto-explorer.md#search-mode)</li></ul> |
|新增索引標籤| 開啟用來查詢 Kusto 的新索引標籤 |

#### <a name="share-section"></a>Share 區段

![Kusto. Explorer 共用區段](./Images/KustoTools-KustoExplorer/home-share-menu.png "共用功能表")

|功能表|    行為|
|----|----------|
|資料到剪貼簿|    將查詢和資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|結果至剪貼簿| 將資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|查詢至剪貼簿| 將查詢匯出至剪貼簿|

#### <a name="visualizations-section"></a>視覺效果區段

![替代文字](./Images/KustoTools-KustoExplorer/home-visualizations-menu.png "功能表-視覺效果")

|功能表         | 行為|
|-------------|---------|
|區域圖      | 顯示一個區域圖，其中 X 軸是第一個資料行（必須是數值），而所有數值資料行都對應到不同的數列（Y 軸） |
|直條圖 | 顯示將所有數值資料行對應至不同數列的直條圖（Y 軸），而在數值為 X 軸之前的文字資料行（可在 UI 中控制）|
|長條圖    | 顯示橫條圖，其中所有數值資料行都對應到不同的數列（X 軸），而文字資料行前面的數值是 Y 軸（可在 UI 中控制）|
|堆疊區域圖      | 顯示堆疊區域圖，其中 X 軸是第一個資料行（必須是數值），而所有數值資料行都對應到不同的數列（Y 軸） |
|時間軸圖表   | 顯示一個時間圖表，其中 X 軸是第一個資料行（必須是 datetime），而所有數值資料行都對應到不同的數列（Y 軸）。|
|折線圖   | 顯示折線圖，其中 X 軸是第一個資料行（必須是數值），而所有數值資料行都對應到不同的數列（Y 軸）。|
|異常圖表|    類似于 timechart，但會使用機器學習異常演算法，尋找時間序列資料中的異常狀況。 對於異常偵測，Kusto 會使用[series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md)函數。(*) 
|圓形圖    |    顯示圓形圖，其中的彩色軸是第一個資料行，而 theta 軸（必須是量值，轉換成百分比）是第二個數據行。|
|階梯圖表 |    顯示「階梯圖表」，其中 X 軸是最後兩個數據行（必須是 datetime），而 Y 軸則是其他資料行的複合。|
|散佈圖| 顯示一個點圖，其中 X 軸是第一個資料行（必須是數值），而所有數值資料行都對應到不同的數列（Y 軸）。|
|樞紐圖表  | Displaya 樞紐分析表和樞紐分析表，提供選取資料、資料行、資料列和各種圖表類型的完整彈性。| 
|時間樞紐分析表   | 在事件時間表上進行互動式導覽（在時間軸上旋轉）|

(*)異常圖表：演算法需要時間序列資料，其中包含兩個數據行：
1. 固定間隔值區中的時間
2. 要在 Kusto 中產生此情況的異常偵測數值，請依 [時間] 欄位摘要，並指定時間值區 bin。

#### <a name="view-section"></a>View 區段

![替代文字](./Images/KustoTools-KustoExplorer/home-view-menu.png "[流覽] 功能表")

|功能表           | 行為|
|---------------|---------|
|完整視圖模式 | 藉由隱藏功能區功能表和連接面板，將工作空間最大化|
|隱藏空白資料行| 從資料格移除空的資料行|
|折迭單數資料行| 折迭具有單數值的資料行|
|探索資料行值| 顯示資料行值分佈|
|增加字型  | 增加 [查詢] 索引標籤和 [結果] 資料格的字型大小|  
|減少字型  | 縮小 [查詢] 索引標籤和 [結果] 資料格的字型大小|

(*)資料檢視設定： Kusto 會持續追蹤每一組唯一資料行所使用的設定。 因此當重新排列或移除資料行時，會儲存資料檢視，並在每次抓取具有相同資料行的資料時重複使用。 若要將視圖重設為預設值，請在 [**視圖**] 索引標籤中選取 [**重設視圖**]。 

### <a name="file-tab"></a>[檔案] 索引標籤

![Kusto. Explorer 檔案](./Images/KustoTools-KustoExplorer/file-tab.png "[檔案]-索引標籤")

|功能表| 行為|
|---------------|---------|
||---------*查詢腳本*---------|
|新增索引標籤 | 開啟新的索引標籤視窗以查詢 Kusto |
|開啟檔案| 將 kql 檔案中的資料載入至作用中的腳本面板|
|儲存至檔案| 將 active script 面板的內容儲存至 *. kql 檔案|
|關閉索引標籤| 關閉目前的索引標籤視窗|
||---------*儲存資料*---------|
|至 CSV       | 將資料匯出至 CSV （逗點分隔值）檔案| 
|至 JSON      | 將資料匯出至 JSON 格式的檔案|
|至 Excel     | 將資料匯出至 .XLSX （Excel）檔案|
|至文字      |    將資料匯出至 TXT （文字）檔案| 
|到 CSL 腳本|    將查詢匯出至腳本檔案| 
|到結果   |    將查詢和資料匯出至結果（QRES）檔案|
||---------*載入資料*---------|
|從結果|    從結果（QRES）檔案載入查詢和資料| 
||---------*粘貼*---------|
|資料到剪貼簿|    將查詢和資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|結果至剪貼簿| 將資料集匯出至剪貼簿。 如果呈現圖表，則會將圖表匯出為點陣圖| 
|查詢至剪貼簿| 將查詢匯出至剪貼簿|
||---------*更*---------|
|清除結果快取| 清除先前執行之查詢的快取結果| 

### <a name="connections-tab"></a>[連接] 索引標籤

![Kusto. Explorer 連接] 索引標籤](./Images/KustoTools-KustoExplorer/connections-tab.png "連接-索引標籤")

|功能表|行為|
|----|----------|
||---------*部門*---------|
|新增群組| 新增 Kusto 伺服器群組|
|重新命名群組| 重新命名現有的 Kusto 伺服器群組|
|移除群組| 移除現有的 Kusto 伺服器群組|
||---------*叢集*---------|
|匯入連接| 從指定連接的檔案匯入連接|
|匯出連接| 匯出檔案的連接|
|新增連接| 新增 Kusto 伺服器連接| 
|編輯連線| 開啟 Kusto 伺服器連接屬性編輯的對話方塊|
|移除連接| 移除 Kusto 伺服器的現有連線|
|Refresh| 重新整理 Kusto 伺服器連接的屬性|
||---------*識別提供者*---------|
|檢查連接主體| 顯示電流作用中使用者詳細資料|
|從 AAD 登出| 登出目前使用者與 AAD 的連線|
||---------*資料範圍*---------|
|快取範圍|<ul><li>僅限[熱資料](../management/cachepolicy.md)快取的熱 DataExecute 查詢</li><li>所有資料：在所有可用的資料上執行查詢（預設值）</li></ul> |
|日期時間資料行| 可能用於時間預先篩選的資料行名稱|
|時間篩選| 時間預先篩選的值|

### <a name="view-tab"></a>[視圖] 索引標籤

![[視圖] 索引標籤](./Images/KustoTools-KustoExplorer/view-tab.png "[視圖]-索引標籤")

|功能表|行為|
|----|----------|
||---------*效果*---------|
|完整視圖模式 | 藉由隱藏功能區功能表和連接面板，將工作空間最大化|
|增加字型  | 增加 [查詢] 索引標籤和 [結果] 資料格的字型大小|  
|減少字型  | 縮小 [查詢] 索引標籤和 [結果] 資料格的字型大小|
|重設版面配置|重設工具的停駐控制項和視窗的版面配置|
||---------*資料檢視*---------|
|重設檢視| 重設資料檢視設定（*）|
|探索資料行值|顯示資料行值分佈|
|專注于查詢統計資料|將焦點變更為查詢統計資料，而不是查詢完成時的查詢結果|
|隱藏重複專案|切換從查詢結果中移除重複的資料列|
|隱藏空白資料行|切換移除查詢結果中的空白資料行|
|折迭單數資料行|切換折迭具有單數值的資料行|
||---------*資料篩選*---------|
|篩選搜尋中的資料列|切換選項，只在查詢結果搜尋中顯示相符的資料列（Ctrl + F）|
||---------*項*---------|
|視覺效果|請參閱上方的[視覺效果](#visualizations-section)。 |

(*)資料檢視設定： Kusto 會持續追蹤每一組唯一資料行所使用的設定。 因此當重新排列或移除資料行時，會儲存資料檢視，並在每次抓取具有相同資料行的資料時重複使用。 若要將視圖重設為預設值，請在 [**視圖**] 索引標籤中選取 [**重設視圖**]。 

### <a name="tools-tab"></a>[工具] 索引標籤

![[工具] 索引標籤](./Images/KustoTools-KustoExplorer/tools-tab.png "工具-索引標籤")

|功能表|行為|
|----|----------|
||---------*感知*---------|
|啟用 IntelliSense| 啟用和停用腳本面板上的 IntelliSense|
||---------*分析*---------|
|Query Analyzer| 啟動 Query Analyzer 工具|
|查詢檢查程式 | 分析目前的查詢並輸出一組適用的改進建議|
|Calculator| 啟動計算機|
||---------*分析*---------|
|分析報表| 開啟儀表板，其中包含多個預先建立的資料分析報表|
||---------*翻譯*---------|
|查詢以 Power BI| 將查詢轉譯成適合在 Power BI 中使用的格式|
||---------*選項*---------|
|重設選項| 將應用程式設定設為預設值|
|選項。| 開啟用來進行應用程式設定的工具。 [詳細資料](kusto-explorer-options.md)|



### <a name="management-tab"></a>[管理] 索引標籤

![[管理] 索引標籤](./Images/KustoTools-KustoExplorer/management-tab.png "管理-tab")

|功能表             | 行為|
|-----------------|---------|
||---------*授權主體*---------|
|管理叢集授權的主體 |為授權的使用者啟用管理叢集主體的功能| 
|管理資料庫授權的主體 | 針對已授權的使用者，啟用管理資料庫的主體| 
|管理已授權的資料表主體 | 為授權的使用者啟用管理資料表的主體| 
|管理已授權的函式主體 | 針對已授權的使用者，啟用管理函式主體的功能| 

### <a name="help-tab"></a>[說明] 索引標籤

![[說明] 索引標籤](./Images/KustoTools-KustoExplorer/help-tab.png "說明-索引標籤")

|功能表             | 行為|
|-----------------|---------|
||---------*附帶*---------|
|説明             | 開啟 Kusto 線上檔的連結  | 
|新增功能       | 開啟一份檔，列出所有 Kusto 的變更|
|報告問題      |開啟包含兩個選項的對話方塊： <ul><li>報告與服務相關的問題</li><li>報告用戶端應用程式中的問題</li></ul> | 
|建議功能  | 開啟 Kusto 意見反應論壇的連結 | 
|檢查更新     | 檢查您的 Kusto 版本是否有更新。 | 

## <a name="connections-panel"></a>連接面板

![替代文字](./Images/KustoTools-KustoExplorer/connectionsPanel.png "連接-面板") 

Kusto 的左窗格會顯示用戶端所設定的所有叢集連接。 它會針對每個叢集顯示其所儲存的資料庫、資料表和屬性（資料行）。 [連線] 面板可讓您選取專案（在主面板中設定搜尋/查詢的隱含內容），或按兩下專案以將名稱複製到 [搜尋/查詢] 面板。

如果實際的架構很大（例如具有數百個數據表的資料庫），您可以按下 CTRL + F 並輸入您要尋找之機構名稱的子字串（不區分大小寫），藉此搜尋架構。

Kusto 支援從查詢視窗控制連接面板。
這對腳本非常有用。 例如，使用下列語法，以指示 Kusto 連接到正在查詢其資料之叢集/資料庫的命令來啟動腳本檔案。 如同往常，您必須使用`F5`或類似的來執行每一行：

```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

### <a name="controlling-the-user-identity-used-for-connecting-to-kusto"></a>控制用來連接到 Kusto 的使用者身分識別

新增新的連線時，所使用的預設安全性模型是 AAD 同盟安全性，其中會使用預設 AAD 使用者體驗，透過 Azure Active Directory 來進行驗證。

在某些情況下，您可能需要比 AAD 中提供的驗證參數更精確的控制。 若是如此，您可以展開 [Advanced： Connection string] 編輯方塊，並提供有效的[Kusto 連接字串](../api/connection-strings/kusto.md)值。

例如，存在於多個 AAD 租使用者中的使用者，有時需要將其身分識別的特定「投射」用於特定 AAD 租使用者。 這可以藉由提供如下所示的連接字串來完成（以特定值取代大寫的單字）：

```
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

唯一的獨特之處`AAD_TENANT_OF_CLUSTER`是，這是託管叢集之 AAD 租使用者的功能變數名稱或 aad 租使用者識別碼（GUID）（通常是擁有叢集的組織功能變數名稱，例如`contoso.com`），而 USER_DOMAIN 是受邀加入該租使用者之使用者的身分識別（例如`joe@fabrikam.com`）。 

>[!Note]
> 使用者的功能變數名稱不一定與主控叢集的租使用者名稱相同。

## <a name="table-row-colors"></a>資料表資料列色彩

Kusto 會嘗試「猜測」結果窗格中每個資料列的嚴重性或詳細等級，並據此進行色彩。 其方式是將每個資料行的相異值與一組已知模式（「警告」、「錯誤」等等）進行比對。

若要修改輸出色彩配置，或關閉此行為，請從 [**工具**] 功能表選取 [**選項** > **] [結果檢視器** > **詳細資訊色彩配置**]。

![替代文字](./Images/KustoTools-KustoExplorer/ke-color-scheme.png)

## <a name="search-mode"></a>搜尋 + + 模式

1. 在 [首頁] 索引標籤的 [查詢] 下拉式清單中，選取 [搜尋 + +]。
2. 選取 [**多個資料表]** ，然後在 **[選擇資料表**] 下，定義要搜尋的資料表。
3. 在編輯方塊中，輸入您的搜尋片語並選取**Go**
4. [資料表/時間位置] 方格的熱度圖會顯示出現的字詞和顯示位置
5. 選取方格中的資料格，然後選取 [ **View Details** ] 以顯示相關專案

![替代文字](./Images/KustoTools-KustoExplorer/ke-search-beta.jpg "ke-搜尋-搶鮮版（Beta）") 

## <a name="query-mode"></a>查詢模式

Kusto 有一個功能強大的腳本模式，可讓您撰寫、編輯和執行臨機操作查詢。 腳本模式隨附語法反白顯示和 IntelliSense，因此您可以快速提升至 Kusto 的 CSL 語言。

### <a name="basic-queries"></a>基本查詢

如果您有資料表記錄，可以藉由輸入下列內容來開始探索：

```kusto
StormEvents | count 
```

當您的游標位於這一行時，它的色彩為灰色。 按 ' F5 ' 會執行查詢。 

以下是一些更多範例查詢：

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

## <a name="importing-a-local-file-into-a-kusto-table"></a>將本機檔案匯入 Kusto 資料表

Kusto 提供便利的方法，將檔案從您的電腦上傳至 Kusto 資料表。

1. 請確定您已建立包含符合檔案之架構的資料表（例如，使用[. create table](../management/tables.md)命令）

1. 請確定副檔名適用于檔案的內容。 例如：
    * 如果您的檔案包含逗號分隔值，請確定您的檔案具有 .csv 副檔名。
    * 如果您的檔案包含定位字元分隔值，請確定您的檔案副檔名為 tsv。

1. 以滑鼠右鍵按一下 [連線][面板](#connections-panel)中的目標**資料庫，然後選取 [** 重新整理]，以顯示您的資料表。

    ![替代文字](./Images/KustoTools-KustoExplorer/right-click-refresh-schema.png "以滑鼠右鍵按一下-重新整理-架構")

1. 以滑鼠右鍵按一下 [[連接] 面板](#connections-panel)中的目標資料表，然後選取 [從本機檔案匯**入資料**]。

    ![替代文字](./Images/KustoTools-KustoExplorer/right-click-import-local-file.png "以滑鼠右鍵按一下-匯入-本機檔案")

1. 選取要上傳的檔案，然後選取 [**開啟**]。

    ![替代文字](./Images/KustoTools-KustoExplorer/import-local-file-choose-files.png "匯入-本機-檔案-選擇檔")

    進度列會顯示進度，並在作業完成時顯示對話方塊

    ![替代文字](./Images/KustoTools-KustoExplorer/import-local-file-progress.png "匯入-本機檔案-進度")

    ![替代文字](./Images/KustoTools-KustoExplorer/import-local-file-complete.png "匯入-本機檔-完成")

1. 查詢資料表中的資料（按兩下 [[連接] 面板](#connections-panel)中的資料表）。

## <a name="managing-authorized-principals"></a>管理授權的主體

Kusto 提供便利的方式來管理叢集、資料庫、資料表或功能授權的主體。

> [!Note]
> 只有系統[管理員](../management/access-control/role-based-authorization.md)可以在自己的範圍中新增或卸載授權的主體。

1. 以滑鼠右鍵按一下 [連線][面板](#connections-panel)中的目標實體，然後選取 [**管理已授權的主體**]。 （您也可以從 [管理] 功能表執行此動作）。

    ![替代文字](./Images/KustoTools-KustoExplorer/right-click-manage-authorized-principals.png "以滑鼠右鍵按一下-[管理]-[授權-主體]")

    ![替代文字](./Images/KustoTools-KustoExplorer/manage-authorized-principals-window.png "管理-授權-主體-視窗")

1. 若要新增已授權的主體，請選取 [**新增主體**]，提供主體詳細資料，然後確認動作。

    ![替代文字](./Images/KustoTools-KustoExplorer/add-authorized-principals-window.png "新增授權-主體-視窗")

    ![替代文字](./Images/KustoTools-KustoExplorer/confirm-add-authorized-principals.png "確認-新增授權-主體")

1. 若要卸載現有的授權主體，請選取 [**捨棄主體**] 並確認動作。

    ![替代文字](./Images/KustoTools-KustoExplorer/confirm-drop-authorized-principals.png "確認-捨棄授權-主體")

## <a name="sharing-queries-and-results-by-email"></a>透過電子郵件共用查詢和結果

Kusto 提供一個便利的方式，讓您透過電子郵件共用查詢和查詢結果。 選取 [**匯出至剪貼**簿]，Kusto 會將下列專案複製到剪貼簿：
1. 您的查詢
1. 查詢結果（資料表或圖表）
1. Kusto 叢集和資料庫的連線詳細資料
1. 會自動重新執行查詢的連結

運作方式如下：

1. 在 Kusto 中執行查詢
1. 選取 [**匯出至剪貼**簿] `Ctrl+Shift+C`（或按）

    ![替代文字](./Images/KustoTools-KustoExplorer/menu-export.png "功能表-匯出")

1. 例如，開啟新的 Outlook 訊息。

    ![替代文字](./Images/KustoTools-KustoExplorer/share-results.png "共用-結果")
    
1. 將剪貼簿的內容貼入 Outlook 訊息。

    ![替代文字](./Images/KustoTools-KustoExplorer/share-results-2.png "共用-結果-2")

## <a name="client-side-query-parametrization"></a>用戶端查詢 parametrization

> [!WARNING]
> Kusto 中有兩種類型的查詢 parametrization 技術：
> * [語言整合式查詢 parametrization](../query/queryparametersstatement.md)會實作為查詢引擎的一部分，並可供以程式設計方式查詢服務的應用程式使用。
>
> * 用戶端查詢 parametrization （如下所述）只是 Kusto 應用程式的一項功能。 這相當於在傳送這些查詢以由服務執行之前，先在查詢上使用字串取代作業。 下面所述的語法不是查詢語言本身的一部分，而且不能用來傳送查詢給服務，方法是使用 Kusto。

如果您打算在多個查詢或多個索引標籤中使用相同的值，就很難以變更它。 不過，Kusto 支援查詢參數。 參數是以{}括弧表示。 例如：`{parameter1}`

腳本編輯器會反白顯示查詢參數：

![替代文字](./Images/KustoTools-KustoExplorer/parametrized-query-1.png "參數化-查詢-1")

您可以輕鬆地定義和編輯現有的查詢參數：

![替代文字](./Images/KustoTools-KustoExplorer/parametrized-query-2.png "參數化-查詢-2")

![替代文字](./Images/KustoTools-KustoExplorer/parametrized-query-3.png "參數化-查詢-3")

腳本編輯器也具有 IntelliSense，適用于已定義的查詢參數：

![替代文字](./Images/KustoTools-KustoExplorer/parametrized-query-4.png "參數化-查詢-4")

可以有多個參數集（列在 [**參數集**] 下拉式方塊中）。
使用 [**加入新**的] 和 [**刪除目前**的] 來指令引數集的清單。

![替代文字](./Images/KustoTools-KustoExplorer/parametrized-query-5.png "參數化-查詢-5")

查詢參數會在索引標籤之間共用，因此可以輕鬆地重複使用。

## <a name="deep-linking-queries"></a>深層連結查詢

### <a name="overview"></a>概觀
您可以建立 URI，在瀏覽器中開啟時，Kusto 會在本機啟動，並在指定的 Kusto 資料庫上執行特定查詢。

### <a name="limitations"></a>限制
查詢限制為 ~ 2000 個字元，因為 Internet Explorer 的限制（限制是因為它依存于叢集和資料庫名稱長度而定） https://support.microsoft.com/kb/208427 ，以減少您將達到字元數限制的機率，請參閱下方的[取得較短的連結](#getting-shorter-links)。

URI 的格式為： HTTPs://<ClusterCname>. kusto.windows.net/<DatabaseName>？ query =<QueryToExecute>

例如：  https://help.kusto.windows.net/Samples?query=StormEvents+%7c+limit+10
 
此 URI 會開啟 Kusto，連接到`help` Kusto 叢集，並在`Samples`資料庫上執行指定的查詢。 如果有 Kusto 的實例已在執行中，則執行中的實例將會開啟新的索引標籤，並在其中執行查詢）。

**安全性注意事項**：基於安全性考慮，已停用控制命令的深層連結。



### <a name="creating-a-deep-link"></a>建立深層連結
建立深層連結最簡單的方式，就是在 Kusto 中撰寫查詢，然後使用 [匯出至剪貼簿]，將查詢（包括深層連結和結果）複製到剪貼簿。 然後您可以透過電子郵件來共用它。
        
複製到電子郵件時，深層連結會以小型字型顯示;例如：

https://help.kusto.windows.net:443/Samples[[按一下以執行查詢](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

第一個連結會開啟 Kusto，並適當地設定叢集和資料庫內容。
第二個連結（按一下以執行查詢）是深層連結。 如果您移至電子郵件訊息的連結，然後按 CTRL-K，您可以看到實際的 URL：

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>深層連結和參數化查詢

您可以使用參數化查詢搭配深層連結。

1. 建立查詢以形成參數化查詢（例如， `KustoLogs | where Timestamp > ago({Period}) | count`） 
2. 在此情況下，請為 URI 中的每個查詢參數提供參數：

`https://mycluster.kusto.windows.net/MyDatabase?web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

### <a name="getting-shorter-links"></a>取得較短的連結

查詢可能會變長。 若要減少查詢超過最大長度的機率，請`String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)`使用 Kusto 用戶端程式庫中提供的方法。 這個方法會產生更精簡的查詢版本。 Kusto 也能辨識較短的格式。

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

藉由套用下一個轉換，讓查詢變得更精簡：

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto. Explorer 命令列引數

Kusto 支援下列語法中的數個命令列引數（順序很重要）：

[*LocalScriptFile*][*QueryString*]

其中：
* *LocalScriptFile*是本機電腦上必須有副檔名`.kql`的指令檔名。 如果這類檔案存在，Kusto 會在啟動時自動載入此檔案。
* *QueryString*是使用 HTTP 查詢字串格式格式化的字串。 這個方法會提供其他屬性，如下表所述。

例如，若要以名`c:\temp\script.kql`為的腳本檔案啟動 Kusto，並設定為與叢集`help`、資料庫`Samples`通訊，請使用下列命令：

```
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|引數  |描述                                                               |
|----------|--------------------------------------------------------------------------|
|**要執行的查詢**                                                                 |
|`query`   |要執行的查詢（base64 編碼）。 如果空白，請`querysrc`使用。          |
|`querysrc`|保存要執行之查詢的檔案或 blob 的 URL （如果`query`是空的）。|
|**連接到 Kusto 叢集**                                                  |
|`uri`     |要連接之 Kusto 叢集的連接字串。                 |
|`name`    |連接至 Kusto 叢集的顯示名稱。                  |
|**連接群組**                                                                 |
|`path`    |要下載之連接群組檔案的 URL （URL 編碼）。             |
|`group`   |連接群組的名稱。                                         |
|`filename`|保留連接群組的本機檔案。                              |

## <a name="kustoexplorer-connection-files"></a>Kusto. Explorer 連接檔案

Kusto 會將`%LOCALAPPDATA%\Kusto.Explorer`其連接設定保留在資料夾中。
連接群組的清單會保留在內部`%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml`，而且每個連接群組都會保留在下`%LOCALAPPDATA%\Kusto.Explorer\Connections\`的專用檔案中。

### <a name="format-of-connection-group-files"></a>連接群組檔案的格式

檔案位置是`%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml`。  

這是具有下列屬性之`ServerGroupDescription`物件陣列的 XML 序列化：

```
  <ServerGroupDescription>
    <Name>`Connection Group name`</Name>
    <Details>`Full path to XML file containing the list of connections`</Details>
  </ServerGroupDescription>
```

範例：

```
<?xml version="1.0"?>
<ArrayOfServerGroupDescription xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerGroupDescription>
    <Name>Connections</Name>
    <Details>C:\Users\alexans\AppData\Local\Kusto.Explorer\UserConnections.xml</Details>
  </ServerGroupDescription>
</ArrayOfServerGroupDescription>  
```

### <a name="format-of-connection-list-files"></a>連接清單檔案的格式

檔案位置為： `%LOCALAPPDATA%\Kusto.Explorer\Connections\`。

這是具有下列屬性之`ServerDescriptionBase`物件陣列的 XML 序列化：

```
   <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>`Connection name`</Name>
    <Details>`Details as shown in UX, usually full URI`</Details>
    <ConnectionString>`Full connection string to the cluster`</ConnectionString>
  </ServerDescriptionBase>
```

範例：

```
<?xml version="1.0"?>
<ArrayOfServerDescriptionBase xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>Help</Name>
    <Details>https://help.kusto.windows.net</Details>
    <ConnectionString>Data Source=https://help.kusto.windows.net:443;Federated Security=True</ConnectionString>
  </ServerDescriptionBase>
</ArrayOfServerDescriptionBase>
```

## <a name="resetting-kustoexplorer"></a>重設 Kusto

如有需要，您可以完全重設 Kusto。 使用下列程式，以漸進方式重設在電腦上部署的 Kusto，直到完全移除該檔案，而且必須從頭開始安裝。

1. 在 Windows 中，開啟 [**變更] 或 [移除程式**] （也稱為 [**程式和功能**]）。
1. 選取名稱開頭為的`Kusto.Explorer`每個專案。
1. 選取 [解除安裝]****。

   如果無法卸載應用程式（有時是 ClickOnce 應用程式的已知問題），請參閱[此堆疊溢位文章](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer)，其中說明如何執行此作業。

1. 刪除資料夾`%LOCALAPPDATA%\Kusto.Explorer`。 這會移除所有連接、歷程記錄等等。

1. 刪除資料夾`%APPDATA%\Kusto`。 這會移除 Kusto token 快取。 您將需要重新驗證所有叢集。

也可以還原為特定版本的 Kusto。

1. 執行 `appwiz.cpl`。
1. 選取 [ **Kusto** ]，然後選取 [**卸載/變更**]。
3. 選取 **[將應用程式還原為先前的狀態**]。

## <a name="troubleshooting"></a>疑難排解

### <a name="kustoexplorer-fails-to-start"></a>Kusto 無法啟動

#### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto 會在啟動期間或之後顯示錯誤對話方塊

**徵兆:**

在啟動時，Kusto 會顯示`InvalidOperationException`錯誤。

**可能的解決方案：**

此錯誤可能會建議作業系統損毀或遺失一些必要的模組。
若要檢查遺失或損毀的系統檔案，請遵循這裡所述的步驟：   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

### <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto 一律會下載，即使沒有更新也一樣

**徵兆:**

每次開啟 Kusto 時，系統會提示您安裝新的新版。 Kusto 會下載整個封裝，而不會實際更新已安裝的版本。

**可能的解決方案：**

這可能是您本機 ClickOnce 存放區損毀的結果。 您可以在提升許可權的命令提示字元中執行下列命令，以清除本機 ClickOnce 存放區。
> [!Important]
> 1. 如果有任何其他 ClickOnce 應用程式或的`dfsvc.exe`實例，請先將其終止，然後再執行此命令。
> 2. 任何 ClickOnce 應用程式會在您下次執行時自動重新安裝，只要您可以存取儲存在應用程式快捷方式中的原始安裝位置即可。 應用程式快捷方式不會被刪除。

```
rd /q /s %userprofile%\appdata\local\apps\2.0
```

請嘗試從其中一個[安裝鏡像](#getting-the-tool)重新安裝 Kusto。

#### <a name="clickonce-error-cannot-start-application"></a>ClickOnce 錯誤：無法啟動應用程式

**徵兆**  

* 程式無法啟動，並顯示錯誤，其中包含：`External component has thrown an exception`
* 程式無法啟動，並顯示錯誤，其中包含：`Value does not fall within the expected range`
* 程式無法啟動，並顯示錯誤，其中包含：`The application binding data format is invalid.` 
* 程式無法啟動，並顯示錯誤，其中包含：`Exception from HRESULT: 0x800736B2`

您可以按一下`Details`下列錯誤對話方塊來流覽錯誤詳細資料：

![替代文字](./Images/KustoTools-KustoExplorer/clickonce-err-1.jpg "clickonce-err-1")

```
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**建議的解決方案步驟：**

1. 使用`Programs and Features` （`appwiz.cpl`）卸載 Kusto. Explorer 應用程式。

1. 請試`CleanOnlineAppCache`著執行，然後再次嘗試安裝 Kusto。 從提高許可權的命令提示字元： 
    
    ```
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    再次從其中一個[安裝鏡像](#getting-the-tool)安裝 Kusto。

1. 如果仍然失敗，請刪除本機 ClickOnce 存放區。 任何 ClickOnce 應用程式會在您下次執行時自動重新安裝，只要您可以存取儲存在應用程式快捷方式中的原始安裝位置即可。 應用程式快捷方式不會被刪除。

從提高許可權的命令提示字元：

    ```
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Install Kusto.Explorer again from one of the [installation mirrors](#getting-the-tool)

1. 如果仍然失敗，請移除暫存部署檔案，並重新命名 Kusto. Explorer 本機 AppData 資料夾。

    從提高許可權的命令提示字元：

    ```
    rd /s/q %userprofile%\AppData\Local\Temp\Deployment
    ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
    ```

    從其中一個[安裝鏡像](#getting-the-tool)重新安裝 Kusto

1. 從已提升許可權的命令提示字元，從 Kusto 還原您的連接：

    ```
    copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
    ```

1. 如果仍然失敗，請在下方建立 LogVerbosityLevel 字串值1，以啟用詳細的 ClickOnce 記錄：

`HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment`，再次重現，並將詳細資訊輸出傳送至KEBugReport@microsoft.com。 

#### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce 錯誤：您的系統管理員已封鎖此應用程式，因為它可能會對您的電腦造成安全性風險

**徵兆:**  
程式安裝失敗，發生下列其中一個錯誤：
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**解決方法：**

1. 這可能是因為另一個應用程式覆寫預設的 ClickOnce 信任提示行為。 您可以查看預設的設定值，並將它們與您電腦上的實際設定進行比較，並視需要重設它們，如[這篇操作說明文章中](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)所述。

#### <a name="cleanup-application-data"></a>清除應用程式資料

有時候，當先前的疑難排解步驟無法協助 Kusto 啟動時，清除儲存在本機的資料可能會有説明。

Kusto 所儲存的資料可在這裡找到： `C:\Users\\[your alias]\AppData\Local\Kusto.Explorer`。

> [!NOTE]
> 清除資料會導致開啟的索引標籤（復原資料夾）、儲存的連接（連接資料夾）和應用程式設定（UserSettings 資料夾）遺失。