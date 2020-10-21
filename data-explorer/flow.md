---
title: 'Azure 資料總管連接器以 Power Automate (預覽) '
description: 瞭解如何使用 Azure 資料總管連接器 Power Automate 來建立自動排程或觸發的工作流程。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 72d092683b490c7b58335abc59fd5e3aea2f3e26
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342938"
---
# <a name="azure-data-explorer-connector-to-power-automate-preview"></a>Azure 資料總管連接器以 Power Automate (預覽) 

Azure 資料總管 Power Automate (之前的 Microsoft Flow) 連接器，可讓 Azure 資料總管使用 [Microsoft Power Automate](https://flow.microsoft.com/)的流程功能。 您可以在已排程或觸發的工作中自動執行 Kusto 查詢和命令。

您可以：

* 傳送包含資料表和圖表的每日報表。
* 根據查詢結果設定通知。
* 排程叢集上的控制命令。
* 在 Azure 資料總管與其他資料庫之間匯出和匯入資料。 

如需詳細資訊，請參閱 [Azure 資料總管 Power Automate 連接器使用範例](flow-usage.md)。

##  <a name="sign-in"></a>登入 

1. 當您第一次連線時，系統會提示您登入。

1. 選取 [登 **入**]，然後輸入您的認證。

![Azure 資料總管登入提示的螢幕擷取畫面](./media/flow/flow-signin.png)

## <a name="authentication"></a>驗證

您可以使用使用者認證進行驗證，或使用 Azure Active Directory (Azure AD) 應用程式進行驗證。

> [!Note]
> 確定您的應用程式是 [Azure AD 的應用程式](./provision-azure-ad-app.md)，而且已獲授權在您的叢集上執行查詢。

1. 在 [執行控制] 命令中，將 **結果視覺化，然後**選取 flow 連接器右上方的三個點。

   ![執行控制命令的螢幕擷取畫面，並將結果視覺化](./media/flow/flow-addconnection.png)

1. 選取**Add new connection**  >  **[使用服務主體加入新的連接連接]**。

   ![Azure 資料總管登入提示字元的螢幕擷取畫面，其中包含 Connect 與服務主體選項](./media/flow/flow-signin.png)

1. 輸入必要資訊：
   - 連接名稱：新連接的描述性且有意義的名稱。
   - 用戶端識別碼：您的應用程式識別碼。
   - 用戶端密碼：您的應用程式金鑰。
   - 租使用者：您在其中建立應用程式之 Azure AD 目錄的識別碼。

   ![[Azure 資料總管應用程式驗證] 對話方塊的螢幕擷取畫面](./media/flow/flow-appauth.png)

驗證完成時，您會看到您的流程使用新增的連接。

![已完成應用程式驗證的螢幕擷取畫面](./media/flow/flow-appauthcomplete.png)

從現在開始，此流程將會使用這些應用程式認證來執行。

## <a name="find-the-azure-kusto-connector"></a>尋找 Azure Kusto 連接器

若要使用 Power Automate 連接器，您必須先新增觸發程式。 您可以根據週期性時間週期來定義觸發程式，或以回應先前的流程動作。

1. [建立新的流程](https://flow.microsoft.com/manage/flows/new)，或從 Microsoft Power Automate 首頁選取 [**我的流程**  >  **+ 新增**]。

    ![Microsoft Power Automate 首頁的螢幕擷取畫面，其中已醒目提示 [我的流程] 和 [新增]](./media/flow/flow-newflow.png)

1. 選取 [ **排程]-從空白**。

    ![[新增] 對話方塊的螢幕擷取畫面，其中已醒目提示空白的排程](./media/flow/flow-scheduled-from-blank.png)

1. 在 [ **建立排程流程**] 中，輸入必要的資訊。
    
    ![[建立排程流程] 頁面的螢幕擷取畫面，其中反白顯示流程名稱選項](./media/flow/flow-build-scheduled-flow.png)

1. 選取 [**建立**  >  **+ 新增步驟**]。
1. 在搜尋方塊中，輸入 *Kusto*，然後選取 [ **Azure 資料總管**]。

    ![選擇 [動作] 選項的螢幕擷取畫面，其中已醒目提示 [搜尋] 方塊和 [Azure 資料總管](./media/flow/flow-actions.png)

## <a name="flow-actions"></a>流程動作

當您開啟 Azure 資料總管連接器時，有三個可能的動作可以新增至您的流程。 本節說明每個動作的功能和參數。

![Azure 資料總管連接器動作的螢幕擷取畫面](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>執行 control 命令並以視覺化方式呈現結果

使用這個動作來執行 [控制項命令](kusto/management/index.md)。

1. 指定叢集 URL。 例如： `https://clusterName.eastus.kusto.windows.net` 。
1. 輸入資料庫的名稱。
1. 指定控制命令：
   * 從流程中使用的應用程式和連接器選取動態內容。
   * 加入運算式以存取、轉換和比較值。
1. 若要透過電子郵件將此動作的結果傳送為數據表或圖表，請指定圖表類型。 這可以是：
   * HTML 資料表。
   * 圓形圖。
   * 時間圖表。
   * 橫條圖。

![[執行控制] 命令的螢幕擷取畫面，並在 [週期] 窗格中將結果視覺化](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> 在 [叢集 **名稱** ] 欄位中，輸入叢集 URL。

### <a name="run-query-and-list-results"></a>執行查詢並列出結果

> [!Note]
> 如果您的查詢是以點開始 (表示它是) 的 [控制項命令](kusto/management/index.md) ，請使用 [ [執行控制] 命令並以視覺化方式呈現結果](#run-control-command-and-visualize-results)。

此動作會將查詢傳送至 Kusto 叢集。 之後加入的動作會逐一查看查詢結果的每一行。

下列範例會每分鐘觸發一次查詢，並根據查詢結果傳送電子郵件。 此查詢會檢查資料庫中的行數，只有在行數大於0時，才會傳送電子郵件。 

![執行查詢和清單結果的螢幕擷取畫面](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 如果資料行有數行，則會針對資料行中的每一行執行連接器。

### <a name="run-query-and-visualize-results"></a>執行查詢並將結果視覺化
        
> [!Note]
> 如果您的查詢是以點開始 (表示它是) 的 [控制項命令](kusto/management/index.md) ，請使用 [ [執行控制] 命令並以視覺化方式呈現結果](#run-control-command-and-visualize-results)。
        
使用此動作可將 Kusto 查詢結果視覺化為數據表或圖表。 例如，使用此流程可透過電子郵件接收每日報表。 
    
在此範例中，查詢的結果會以 HTML 資料表的形式傳回。
            
![執行查詢並將結果視覺化的螢幕擷取畫面](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> 在 [叢集 **名稱** ] 欄位中，輸入叢集 URL。

## <a name="email-kusto-query-results"></a>電子郵件 Kusto 查詢結果

您可以在任何流程中包含步驟，以電子郵件將報表傳送至任何電子郵件地址。 

1. 選取 [ **+ 新增步驟** ]，將新步驟新增至流程。
1. 在 [搜尋] 方塊中，輸入 *office 365* ，然後選取 [ **office 365 Outlook**]。
1. 選取 [ **傳送電子郵件 (V2) **。
1. 輸入您想要傳送電子郵件報告的電子郵件地址。
1. 輸入電子郵件的主旨。
1. 選取 [程式 **代碼視圖**]。
1. **將游標放在 [內**文] 欄位中，然後選取 [**新增動態內容**]。
1. 選取 [ **BodyHtml**]。
    ![[傳送電子郵件] 對話方塊的螢幕擷取畫面，其中已醒目提示主體欄位和 BodyHtml](./media/flow/flow-send-email.png)
1. 選取 [顯示進階選項]****。
1. 在 [ **附件名稱-1**] 底下，選取 [ **附件名稱**]。
1. 在 [ **附件內容**] 底下，選取 [ **附件內容**]。
1. 如有必要，請新增更多的附件。 
1. 如有必要，請設定重要性層級。
1. 選取 [儲存]。

![[傳送電子郵件] 對話方塊的螢幕擷取畫面，其中已醒目提示 [附件名稱]、[附件內容] 和 [儲存]](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>檢查您的流程是否成功

若要檢查您的流程是否成功，請參閱流程的執行歷程記錄：
1. 移至 [Microsoft Power Automate 首頁](https://flow.microsoft.com/)。
1. 從主功能表選取 [ [我的流程](https://flow.microsoft.com/manage/flows)]。
   
   ![Microsoft Power Automate 主功能表的螢幕擷取畫面，其中反白顯示我的流程](./media/flow/flow-myflows.png)

1. 在您想要調查之流程的資料列上，選取 [其他命令] 圖示，然後選取 [ **執行歷程記錄**]。

    ![[流程] 索引標籤的螢幕擷取畫面，醒目提示 [執行歷程記錄]](./media/flow//flow-runhistory.png)

    系統會列出所有流程執行，包含開始時間、持續時間和狀態的相關資訊。
    ![[執行歷程記錄結果] 頁面的螢幕擷取畫面](./media/flow/flow-runhistoryresults.png)

    如需流程的完整詳細資訊，請在 [ **[我的流程](https://flow.microsoft.com/manage/flows)**] 上，選取您要調查的流程。

    ![[執行歷程記錄完整結果] 頁面的螢幕擷取畫面](./media/flow/flow-fulldetails.png) 

若要查看執行失敗的原因，請選取 [執行開始時間]。 流程隨即出現，而失敗的流程步驟則以紅色驚嘆號表示。 展開失敗的步驟以查看其詳細資料。 右側的 [ **詳細資料** ] 窗格包含失敗的相關資訊，讓您可以對其進行疑難排解。

![[流程錯誤] 頁面的螢幕擷取畫面](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Timeout 例外狀況

如果您的流程執行超過七分鐘，您的流程可能會失敗，並傳回「RequestTimeout」例外狀況。
    
![流程要求超時例外狀況錯誤的螢幕擷取畫面](./media/flow/flow-requesttimeout.png)

若要修正超時問題，請讓查詢更有效率，讓它執行得更快，或將它分成多個區塊。 每個區塊都可以在查詢的不同部分上執行。 如需詳細資訊，請參閱 [查詢最佳做法](kusto/query/best-practices.md)。

相同的查詢可能會在 Azure 資料總管中成功執行，其中的時間不受限且可以變更。

## <a name="limitations"></a>限制

* 傳回給用戶端的結果會限制為500000筆記錄。 這些記錄的整體記憶體不能超過 64 MB，而且會有七分鐘的時間來執行。
* 連接器不支援 [分叉](kusto/query/forkoperator.md) 和 [facet](kusto/query/facetoperator.md) 運算子。
* Flow 最適合 Microsoft Edge 和 Google Chrome。

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure Kusto 邏輯應用程式連線程式](kusto/tools/logicapps.md)，這是在排程或觸發的工作中，自動執行 Kusto 查詢和命令的另一種方式。