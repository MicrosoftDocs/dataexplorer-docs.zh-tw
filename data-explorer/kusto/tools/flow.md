---
title: 微軟流 Azure 函式庫斯托連接器(預覽) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Microsoft 流 Azure 庫斯托連接器(預覽)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: c4e732afb9e6165f803b9dc7e801b4e2be4c2588
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504092"
---
# <a name="microsoft-flow-azure-kusto-connector-preview"></a>微軟流 Azure 函式庫斯托連接器(預覽版)

Microsoft Flow Azure Kusto 連接器允許使用者使用[Microsoft Flow](https://flow.microsoft.com/)自動執行 Kusto 查詢和命令作為計畫或觸發任務的一部分。

常見使用機制包括:

* 傳送包含表格和圖表的每日報告
* 依查詢結果設定通知
* 在叢集上調度控制命令
* Azure 資料資源管理員與資料庫之間匯出及匯入資料 

## <a name="limitations"></a>限制

1. 返回給客戶端的結果僅限於 500,000 條記錄。 這些記錄的總記憶體不能超過 64 MB 和 7 分鐘執行時間。
2. 目前,連接器不支援[分叉](../query/forkoperator.md)和[分面](../query/facetoperator.md)運算符。
3. 流量在互聯網瀏覽器和Chrome上效果最佳。

##  <a name="login"></a>登入 

1. 登入[微軟流](https://flow.microsoft.com/)。

1. 首次連接到 Azure Kusto 連接器時,系統將提示您登入。

1. 按下「**登入**」按鈕並輸入認證以開始使用 Azure Kusto 流。

![alt 文字](./Images/KustoTools-Flow/flow-signin.png "流登錄")

## <a name="authentication"></a>驗證

可以使用使用者認證或 AAD 應用程式對 Azure Kusto 串流連接器進行身分驗證。



### <a name="aad-application-authentication"></a>AAD 應用程式身份驗證

您可以使用以下步驟使用 AAD 應用程式對 Azure Kusto Flow 進行身份驗證:

> 注意:確保您的應用程式是[AAD 應用程式](../management/access-control/how-to-provision-aad-app.md),並有權在群集上執行查詢。

1. 點選 Azure Kusto 連接器右上角的三個點:![取代文字](./Images/KustoTools-Flow/flow-addconnection.png "流新增連線")

1. 選擇"添加新連接",然後單擊"與服務主體連接"。
![alt 文字](./Images/KustoTools-Flow/flow-signin.png "流登錄")

1. 填寫應用程式 ID、應用程式金鑰和租戶 ID。

例如,Microsoft 租戶 ID 是:72f988bf-86f1-41af-91ab-2d7cd011db47。
連接名稱值是您為識別添加的新連接而選擇的字串。
![alt 文字](./Images/KustoTools-Flow/flow-appauth.png "流-appauth")

身份驗證完成後,您將能夠看到流正在使用添加的新連接。
![alt 文字](./Images/KustoTools-Flow/flow-appauthcomplete.png "流-應用完成")

從現在起,此流將使用應用程式憑據運行。

## <a name="find-the-azure-kusto-connector"></a>尋找 Azure 函式庫斯托連接器

要使用 Azure Kusto 連接器,需要首先添加觸發器。 可以基於定期時間段或對前一個流操作的回應定義觸發器。

1. [創建新流。](https://flow.microsoft.com/manage/flows/new)
2. 添加"計劃 - 重複"作為第一步。
3. 在第二步搜索框中鍵入「Azure Kusto」。。

現在,您應該能夠看到下圖中的"Azure 庫塞托"。

![alt 文字](./Images/KustoTools-Flow/flow-actions.png "流操作")

## <a name="azure-kusto-flow-actions"></a>Azure 函式庫斯托流操作

在 Flow 中搜尋 Azure Kusto 連接器時,您將看到可以添加到流中的 3 種可能操作。

以下部分介紹每個 Azure Kusto Flow 操作的功能和參數。

![alt 文字](./Images/KustoTools-Flow/flow-actions.png "流操作")

### <a name="azure-kusto---run-query-and-visualize-results"></a>Azure 函式庫塞托 - 執行查詢並可視化結果

> 注意:如果查詢以點開頭(表示它是[控制命令](../management/index.md)),請使用[「執行控制」命令並可視化結果](./flow.md#azure-kusto---run-control-command-and-visualize-results)

要將 Kusto 查詢結果可視化為表或圖表,可以使用「Azure Kusto - 運行查詢並可視化結果」操作。 此操作的結果稍後可以通過電子郵件發送。 例如,您可以使用此流,以便獲取每日 ICM 報告。 

![alt 文字](./Images/KustoTools-Flow/flow-runquery.png "流執行查詢")

在此範例中,查詢的結果將作為 HTML 表返回。

### <a name="azure-kusto---run-control-command-and-visualize-results"></a>Azure 函式庫塞托 - 執行控制命令並可視化結果

與「Azure Kusto - 執行查詢並可視化結果」操作類似,您還可以使用「Azure Kusto - 執行控制命令」操作執行[控制命令](../management/index.md)並可視化結果。
此操作的結果稍後可以通過電子郵件發送為表格或圖表。

![alt 文字](./Images/KustoTools-Flow/flow-runcontrolcommand.png "串流執行控制命令")

在此示例中,控件命令的結果呈現為餅圖。

### <a name="azure-kusto---run-query-and-list-results"></a>Azure 函式庫斯特 - 執行查詢及清單結果

> 注意:如果您的查詢以點開頭(意味著它是[控制命令](../management/index.md)),請使用[「執行控制」命令並可視化結果](./flow.md#azure-kusto---run-control-command-and-visualize-results)

此操作向 Kusto 群集發送查詢。 之後添加的操作會反覆運算查詢結果的每一行。

以下示例每分鐘觸發查詢,並根據查詢結果發送電子郵件。 查詢檢查資料庫中的行數,然後僅在行數大於 0 時發送電子郵件。 

![alt 文字](./Images/KustoTools-Flow/flow-runquerylistresults.png "串流執行查詢清單結果")

請注意,如果列有多個行,則以下連接器將運行該列中的每行。

## <a name="email-kusto-query-results"></a>透過電子郵件傳送函式庫斯托查詢結果

要傳送電子郵件報告,請執行以下步驟: 

![alt 文字](./Images/KustoTools-Flow/flow-sendemail.png "流傳送電子郵件")

1. 按下"+ 新步驟",然後單擊"添加操作"。
2. 在搜尋框中,輸入"Office 365 Outlook - 發送電子郵件"。
3. 將"To"設置為您的電子郵件位址,將"主題"設置為某些文本,並將動態內容中的"正文"添加到"正文"欄位中。
4. 按下「進階選項」將「附件名稱」添加到「附件名稱」欄位,「附件內容」添加到「附件內容」欄位,並確保"HTML"設定為"是"。
5. 在頂部欄處,設置此流的"流名稱"。
6. 按下「創建流」,您就完成了!

## <a name="how-to-make-sure-flow-succeeded"></a>如何確保流成功?

轉到[微軟流主頁](https://flow.microsoft.com/),點擊["我的流",](https://flow.microsoft.com/manage/flows)然後單擊 i 按鈕。

![alt 文字](./Images/KustoTools-Flow/flow-myflows.png "my流")

所有流運行都列出狀態、開始時間和持續時間。

![alt 文字](./Images/KustoTools-Flow/flow-runs.png "流運行")

打開流的最後一次運行時,如果流的所有步驟都標有綠色 V,則流已成功結束。
否則,展開標有紅色感嘆號的步驟以查看錯誤詳細資訊。

![alt 文字](./Images/KustoTools-Flow/flow-failed.png "流失敗")

某些錯誤可以自行輕鬆解決,例如查詢語法錯誤:

![alt 文字](./Images/KustoTools-Flow/flow-syntaxerror.png "串流文法錯誤")



## <a name="having-a-timeout-exception"></a>有超時異常?

如果流運行超過 7 分鐘,則可能會失敗並返回"請求超時"異常。

請注意,7 分鐘是流查詢在出現超時異常之前可以運行的最大時間。

[按一次查看](#limitations)Microsoft 流限制。

相同的查詢可以在 Kusto 資源管理器中成功運行,因為時間不受限制,可以更改。

下圖顯示了「請求超時」異常:

![alt 文字](./Images/KustoTools-Flow/flow-requesttimeout.png "流要求逾時")

要解決此問題,您可以按照以下步驟操作:
1. 閱讀有關[查詢最佳實務的更多內容](../query/best-practices.md)。
2. 嘗試提高查詢的效率,以便使其運行得更快,或將其劃分為塊,每個區塊都可以在查詢的不同部分運行。

## <a name="usage-examples"></a>使用方式範例

本節包含使用 Azure Kusto 流連接器的幾個常見示例。

### <a name="example-1---azure-kusto-flow-and-sql"></a>範例 1 - Azure 函式庫斯托流和 SQL

可以使用 Azure Kusto 流來查詢數據,然後將其累積到 SQL DB 中。 
> 注意:SQL 插入每行單獨完成,請僅對少量輸出數據使用。 

![alt 文字](./Images/KustoTools-Flow/flow-sqlexample.png "流-sql 範例")

### <a name="example-2---push-data-to-power-bi-dataset"></a>範例 2 - 將資料推送到 Power BI 資料集

Azure Kusto 串流連接器可與 Power BI 連接器一起使用,將數據從 Kusto 查詢推送到 Power BI 串流資料集。

首先,創建一個新的"Kusto - 運行查詢和列表結果" 操作。

![alt 文字](./Images/KustoTools-Flow/flow-listresults.png "串流清單結果")

按下「新步驟」,選擇「添加操作」並搜索「Power BI」。。 按下「Power BI - 將行添加到資料集」。 

![alt 文字](./Images/KustoTools-Flow/flow-powerbiconnector.png "流量功率連接器")

填寫要推送數據的工作區、數據集和表。
添加包含資料集架構和動態內容視窗中相關 Kusto 查詢結果的有效負載。 

![alt 文字](./Images/KustoTools-Flow/flow-powerbifields.png "流動力場")

請注意,Flow 將自動為 Kusto 查詢結果表的每一行應用 Power BI 操作。 

![alt 文字](./Images/KustoTools-Flow/flow-powerbiforeach.png "流量-動力-每個")

### <a name="example-3---conditional-queries"></a>範例 3 - 條件查詢

Kusto 查詢的結果可用作下一個 Flow 操作的輸入或條件。

在下面的示例中,我們查詢 Kusto 的最後一天發生的事件。 對於已解決的每個事件,我們會發佈有關該事件的鬆弛消息,並創建推送通知。
對於仍然處於活動狀態的每個事件,我們查詢 Kusto 以獲取有關類似事件的詳細資訊,將該資訊作為電子郵件發送並打開相關的 TFS 任務。

按照這些說明創建類似的流。

建立新的「Kusto - 執行查詢和列表結果」 操作。
單擊"新步驟"並選擇"添加條件" 

![alt 文字](./Images/KustoTools-Flow/flow-listresults.png "串流清單結果")

從動態內容視窗中選擇要用作下一步操作的條件的參數。
選擇關係和值的類型以在給定參數上設置特定條件。

![alt 文字](./Images/KustoTools-Flow/flow-condition.png "流狀況")

請注意,Flow 將在查詢結果表的每一行上應用此條件。

添加條件為真假時的操作。

![alt 文字](./Images/KustoTools-Flow/flow-conditionactions.png "串流條件操作")

您可以通過從動態內容視窗選擇來自 Kusto 查詢的結果值作為下一個操作的輸入。
下面我們添加了"鬆弛 - 消息后"操作和"可視化工作室 - 創建新的工作項"操作,其中包含來自 Kusto 查詢的數據。

![alt 文字](./Images/KustoTools-Flow/flow-slack.png "流鬆弛")

![alt 文字](./Images/KustoTools-Flow/flow-visualstudio.png "流-視覺工作室")

在此示例中,如果事件仍然處於活動狀態,我們會再次查詢 Kusto,以獲取有關過去如何解決來自同一源的事件的資訊。

![alt 文字](./Images/KustoTools-Flow/flow-conditionquery.png "串流條件查詢")

我們將此資訊可視化為餅圖,並將其通過電子郵件發送給我們的團隊。

![alt 文字](./Images/KustoTools-Flow/flow-conditionemail.png "串流狀態電子郵件")

### <a name="example-4---email-multiple-azure-kusto-flow-charts"></a>範例 4 - 透過電子郵件傳送多個 Azure 函式庫斯托流程圖

使用"重複"觸發器創建新的 Flow,並定義流的間隔和頻率。 

添加新步驟,包含一個或多個"Kusto - 運行查詢並可視化結果"操作。 

![alt 文字](./Images/KustoTools-Flow/flow-severalqueries.png "流數查詢")

對每個 Kusto - 執行查詢並可檢視結果「定義以下欄位:

叢集名稱、資料庫名稱、查詢和圖表類型(Html 表格/圓形圖/時間圖表/條形圖/輸入自定義值)。

![alt 文字](./Images/KustoTools-Flow/flow-visualizeresultsmultipleattachments.png "串流視覺化結果多個附件")

完成「Kusto - 執行查詢並可視化結果」操作後,添加「發送電子郵件」操作。 

請確保在「正文」欄位中插入所需的正文,以便將查詢的可視化結果附加到電子郵件正文。
此外,為了向電子郵件添加附件,請添加「附件名稱」和「附件內容」。

請確保在「是 HTML 欄位下選擇」 的。

![alt 文字](./Images/KustoTools-Flow/flow-emailmultipleattachments.png "串流電子郵件多個附件")

結果：

![alt 文字](./Images/KustoTools-Flow/flow-resultsmultipleattachments.png "串流-結果多個附件")

![alt 文字](./Images/KustoTools-Flow/flow-resultsmultipleattachments2.png "串流-結果多個附件2")

### <a name="example-5---send-a-different-email-to-different-contacts"></a>範例 5 - 傳送不同的電子郵件

可以利用 Azure 庫托流向不同的聯繫人發送不同的自訂電子郵件。 電子郵件地址以及電子郵件內容是 Kusto 查詢的結果。

請參閱以下範例：

![alt 文字](./Images/KustoTools-Flow/flow-dynamicemailkusto.png "串流動態電子郵件庫托")

![alt 文字](./Images/KustoTools-Flow/flow-dynamicemail.png "串流動態電子郵件")

### <a name="example-6---create-custom-html-table"></a>範例 6 - 建立自訂 HTML 表

可以利用 Azure Kusto 流創建和使用自訂 HTML 元素(如自訂 HTML 表)。

下面的範例展示如何建立自訂 HTML 表。 HTML 表的行將由日誌等級著色(與 Kusto 資源管理器中相同)。

以以下說明建立類似的流:

建立新的「Kusto - 執行查詢和列表結果」 操作。

![alt 文字](./Images/KustoTools-Flow/flow-listresultforhtmltable.png "串流清單結果為 htmltable")

循環查詢結果並建立 HTML 表正文。 首先,創建一個包含 HTML 字串的變數 -按一下"新步驟",選擇"添加操作"並搜尋"變數"。 按下"變數 -初始化變數"。 初始化字串變數,如下所示:

![alt 文字](./Images/KustoTools-Flow/flow-initializevariable.png "串流初始化可變")

循環查看結果 - 按下"新步驟"並選擇"添加操作"。 搜索"變數"。 選擇"變數 - 追加字串變數" 選擇之前初始化的變數名稱,並使用查詢結果創建 HTML 表行。 選擇查詢結果時,將添加"應用於每個查詢結果」。。

在下面的範例中,如果使用運算式定義每行的樣式,則如下所示:

如果(等(專案(Apply_to_each'??級別"","警告","黃色",如果(等於(專案(Apply_to_each')??級別*,"錯誤","紅色","白色"))

![alt 文字](./Images/KustoTools-Flow/flow-createhtmltableloopcontent.png "串流-建立-可表循環內容")

最後,創建完整的 HTML 內容。 在「應用於每個操作」之外添加新操作。 在下面的示例中,使用的操作是「發送電子郵件」。 使用上述步驟中的變數定義 HTML 表。 如果您要發送電子郵件,請按兩下"顯示高級選項",並在"是 HTML"下選擇"是"

![alt 文字](./Images/KustoTools-Flow/flow-customhtmltablemail.png "流自訂電子郵件")

結果是:

![alt 文字](./Images/KustoTools-Flow/flow-customhtmltableresult.png "串流自訂可搜尋結果")






