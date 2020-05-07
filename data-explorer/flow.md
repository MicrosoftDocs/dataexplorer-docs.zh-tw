---
title: Azure 資料總管連接器對電源自動化（預覽）
description: 瞭解如何使用 Azure 資料總管連接器來進行自動化，以建立自動排程或觸發工作的流程。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 57f3ae587f449739f7e3aaaa0aa817530447097e
ms.sourcegitcommit: 98eabf249b3f2cc7423dade0f386417fb8e36ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868735"
---
# <a name="azure-data-explorer-connector-to-power-automate-preview"></a>Azure 資料總管連接器對電源自動化（預覽）

Azure 資料總管 flow 連接器可讓 Azure 資料總管使用[Microsoft 電源自動化](https://flow.microsoft.com/)的流程功能。 您可以在已排程或觸發的工作中，自動執行 Kusto 查詢和命令。

您可以：

* 傳送包含資料表和圖表的每日報表。
* 根據查詢結果設定通知。
* 在叢集上排程式控制制命令。
* 在 Azure 資料總管與其他資料庫之間匯出和匯入資料。 

如需詳細資訊，請參閱[Azure 資料總管流程連接器使用範例](flow-usage.md)。

##  <a name="sign-in"></a>登入 

1. 當您第一次連線時，系統會提示您登入。

1. 選取 [登**入**]，然後輸入您的認證。

![Azure 資料總管登入提示的螢幕擷取畫面](./media/flow/flow-signin.png)

## <a name="authentication"></a>驗證

您可以使用使用者認證或 Azure Active Directory （Azure AD）應用程式進行驗證。

> [!Note]
> 請確定您的應用程式是[Azure AD 的應用程式](kusto/management/access-control/how-to-provision-aad-app.md)，並已獲授權可在您的叢集上執行查詢。

1. 在 [**執行控制命令] 和 [視覺化結果**] 中，選取流程連接器右上方的三個點。

   ![執行控制命令和視覺化結果的螢幕擷取畫面](./media/flow/flow-addconnection.png)

1. 選取 [**新增** > 連線**與服務主體的連接]**。

   ![Azure 資料總管登入提示的螢幕擷取畫面，使用 [使用服務主體連線] 選項](./media/flow/flow-signin.png)

1. 輸入必要資訊：
   - 連接名稱：新連接的描述性且有意義的名稱。
   - 用戶端識別碼：您的應用程式識別碼。
   - 用戶端密碼：您的應用程式金鑰。
   - 租使用者：您用來建立應用程式之 Azure AD 目錄的識別碼。

   ![[Azure 資料總管應用程式驗證] 對話方塊的螢幕擷取畫面](./media/flow/flow-appauth.png)

當驗證完成時，您會看到您的流程使用新加入的連接。

![已完成應用程式驗證的螢幕擷取畫面](./media/flow/flow-appauthcomplete.png)

從現在開始，此流程將會使用這些應用程式認證來執行。

## <a name="find-the-azure-kusto-connector"></a>尋找 Azure Kusto 連接器

若要使用 flow 連接器，您必須先新增觸發程式。 您可以根據週期性時間週期來定義觸發程式，或根據先前的流程動作來回應。

1. [建立新流程](https://flow.microsoft.com/manage/flows/new)，或從 Microsoft 電源自動化首頁選取 [**我的流程** > **+ 新增**]。

    ![Microsoft 電源自動化首頁的螢幕擷取畫面，其中包含 [我的流程] 和 [新增] 反白顯示](./media/flow/flow-newflow.png)

1. 選取 [已**排程--從空白**]。

    ![[新增] 對話方塊的螢幕擷取畫面，其中已醒目提示 [已反白顯示]](./media/flow/flow-scheduled-from-blank.png)

1. 在 [**建立排程的流程**] 中，輸入必要的資訊。
    
    ![[建立排程的流程] 頁面的螢幕擷取畫面，其中已反白顯示流程名稱選項](./media/flow/flow-build-scheduled-flow.png)

1. 選取 [**建立** > **+ 新增步驟**]。
1. 在搜尋方塊中，輸入*Kusto*，然後選取 [ **Azure 資料總管**]。

    ![[選擇動作選項] 的螢幕擷取畫面，其中已醒目提示 [搜尋] 方塊和 [Azure 資料總管]](./media/flow/flow-actions.png)

## <a name="flow-actions"></a>流程動作

當您開啟 Azure 資料總管連接器時，有三個可能的動作可新增至您的流程。 本節說明每個動作的功能和參數。

![Azure 資料總管連接器動作的螢幕擷取畫面](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>執行控制命令並以視覺化方式呈現結果

使用此動作來執行[控制命令](kusto/management/index.md)。

1. 指定叢集 URL。 例如： `https://clusterName.eastus.kusto.windows.net` 。
1. 輸入資料庫的名稱。
1. 指定 [控制] 命令：
   - 從流程中使用的應用程式和連接器選取動態內容。
   - 加入運算式來存取、轉換和比較值。
1. 若要透過電子郵件將此動作的結果傳送為數據表或圖表，請指定圖表類型。 這可以是：
   - HTML 資料表。
   - 圓形圖。
   - 時間圖表。
   - 橫條圖。

![執行控制命令和視覺化結果的螢幕擷取畫面](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> 在 [叢集**名稱**] 欄位中，輸入叢集 URL。

### <a name="run-query-and-list-results"></a>執行查詢並列出結果

> [!Note]
> 如果您的查詢是以點（表示它是[control 命令](kusto/management/index.md)）開頭，請使用 [[執行控制] 命令並將結果視覺化](#run-control-command-and-visualize-results)。

此動作會將查詢傳送至 Kusto 叢集。 之後加入的動作會逐一查看查詢結果的每一行。

下列範例會每分鐘觸發一次查詢，並根據查詢結果傳送電子郵件。 查詢會檢查資料庫中的行數，只有當行數大於0時，才會傳送電子郵件。 

![執行查詢和列出結果的螢幕擷取畫面](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 如果資料行有數行，則連接器會針對資料行中的每一行執行。

### <a name="run-query-and-visualize-results"></a>執行查詢並將結果視覺化
        
> [!Note]
> 如果您的查詢是以點（表示它是[control 命令](kusto/management/index.md)）開頭，請使用 [[執行控制] 命令並將結果視覺化](#run-control-command-and-visualize-results)。
        
使用此動作將 Kusto 查詢結果視覺化為數據表或圖表。 例如，使用此流程以電子郵件接收每日報告。 
    
在此範例中，查詢的結果會以 HTML 資料表的形式傳回。
            
![執行查詢和視覺化結果的螢幕擷取畫面](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> 在 [叢集**名稱**] 欄位中，輸入叢集 URL。

## <a name="email-kusto-query-results"></a>Kusto 查詢結果的電子郵件

您可以在任何流程中包含步驟，以透過電子郵件將報表傳送至任何電子郵件地址。 

1. 選取 [ **+ 新增步驟**]，將新步驟新增至您的流程。
1. 在 [搜尋] 方塊中，輸入*office 365* ，然後選取 [ **office 365 Outlook**]。
1. 選取 **[傳送電子郵件（V2）**]。
1. 輸入您要傳送電子郵件報表的目標電子郵件地址。
1. 輸入電子郵件的主旨。
1. 選取 [程式**代碼視圖**]。
1. **將游標放在 [內**文] 欄位中，然後選取 [**新增動態內容**]。
1. 選取 [ **BodyHtml**]。
    ![[傳送電子郵件] 對話方塊的螢幕擷取畫面，其中的 [內文] 欄位和 BodyHtml 反白顯示](./media/flow/flow-send-email.png)
1. 選取 [顯示進階選項]****。
1. 在 [**附件名稱-1**] 底下，選取 [**附件名稱**]。
1. 在 [**附件內容**] 底下，選取 [**附件內容**]。
1. 如有需要，請新增更多附件。 
1. 如有必要，請設定重要性層級。
1. 選取 [儲存]  。

![[傳送電子郵件] 對話方塊的螢幕擷取畫面，其中包含附件名稱、附件內容，以及醒目提示的儲存](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>檢查您的流程是否成功

若要檢查您的流程是否成功，請參閱流程的執行歷程記錄：
1. 前往[Microsoft 電源自動化](https://flow.microsoft.com/)首頁。
1. 從主功能表中，選取 [[我的流程](https://flow.microsoft.com/manage/flows)]。
   
   ![Microsoft 電源自動化主功能表的螢幕擷取畫面，其中反白顯示 [我的流程]](./media/flow/flow-myflows.png)

1. 在您要調查之流程的資料列上，選取 [更多命令] 圖示，然後選取 [**執行歷程記錄**]。

    ![[我的流程] 索引標籤的螢幕擷取畫面，其中已反白顯示](./media/flow//flow-runhistory.png)

    所有流程執行都會列出，其中包含開始時間、持續時間和狀態的相關資訊。
    ![[執行歷程記錄結果] 頁面的螢幕擷取畫面](./media/flow/flow-runhistoryresults.png)

    如需流程的完整詳細資料，請在 [**[我的流程](https://flow.microsoft.com/manage/flows)**] 中，選取您要調查的流程。

    ![[執行歷程記錄] [完整結果] 頁面的螢幕擷取畫面](./media/flow/flow-fulldetails.png) 

若要查看執行失敗的原因，請選取 [執行開始時間]。 流程隨即出現，而失敗流程的步驟會以紅色驚嘆號表示。 展開失敗的步驟，以查看其詳細資料。 右側的 [**詳細資料**] 窗格包含失敗的相關資訊，讓您可以對其進行疑難排解。

![[流量錯誤] 頁面的螢幕擷取畫面](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>超時例外狀況

您的流程可能會失敗，並傳回 "RequestTimeout" 例外狀況（如果執行超過七分鐘）。
    
![流程要求超時例外狀況錯誤的螢幕擷取畫面](./media/flow/flow-requesttimeout.png)

若要修正超時問題，請讓查詢更有效率，使其執行得更快，或將其分隔成區塊。 每個區塊都可以在查詢的不同部分執行。 如需詳細資訊，請參閱[查詢最佳做法](kusto/query/best-practices.md)。

相同的查詢可能會在 Azure 資料總管中成功執行，其中的時間不受限制，而且可以變更。

## <a name="limitations"></a>限制

* 傳回給用戶端的結果僅限於500000記錄。 這些記錄的整體記憶體不能超過 64 MB，而且需要七分鐘的時間來執行。
* 連接器不支援[分叉](kusto/query/forkoperator.md)和[facet](kusto/query/facetoperator.md)運算子。
* Flow 在 Microsoft Edge 和 Google Chrome 上的效果最佳。

## <a name="next-steps"></a>後續步驟

瞭解[Azure Kusto 邏輯應用程式連接器](kusto/tools/logicapps.md)，這是在排定或觸發的工作中，可自動執行 Kusto 查詢和命令的另一種方式。
