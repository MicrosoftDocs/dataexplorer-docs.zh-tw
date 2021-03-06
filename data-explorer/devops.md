---
title: 適用于 Azure 資料總管的 Azure DevOps 工作
description: 在本主題中，您將瞭解如何建立發行管線並部署
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/05/2019
ms.openlocfilehash: 14ba0226efc5f38ef3d549f38b2a6224da7c201e
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320718"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>適用于 Azure 資料總管的 Azure DevOps 工作

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) 提供開發共同作業工具，例如高效能管線、免費私用 Git 儲存機制、可設定的看板面板，以及廣泛的自動化和持續測試功能。 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 是一種 Azure DevOps 的功能，可讓您管理 CI/CD，以使用適用于任何語言、平臺和雲端的高效能管線來部署程式碼。
[Azure 資料總管-系統管理員命令](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) 是 Azure Pipelines 工作，可讓您建立發行管線，並將資料庫變更部署到您的 Azure 資料總管資料庫。 這項功能可在 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)免費使用。

本檔說明如何使用 **Azure 資料總管–管理員命令** 工作將架構變更部署至資料庫的簡單範例。 如需完整的 CI/CD 管線，請參閱 [Azure DevOps 檔](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* Azure 資料總管叢集設定：
    * [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
    * 藉由布建 [Azure AD 應用程式](./provision-azure-ad-app.md)，建立 Azure Active Directory (Azure AD) 應用程式。
    * 藉由 [管理 azure 資料總管資料庫許可權](manage-database-permissions.md)，將存取權授與 azure 資料總管資料庫上的 Azure AD App。
* Azure DevOps 設定：
    * [註冊免費組織](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [建立組織](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [在 Azure DevOps 中建立專案](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [使用 Git 撰寫程式碼](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>建立資料夾

在您的 Git 存放庫中 *， (函* 式、 *原則*、 *資料表*) 建立下列範例資料夾。 從 [這裡](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) 將檔案複製到個別的資料夾，如下所示，並認可變更。 系統會提供範例檔案來執行下列工作流程。

![建立資料夾](media/devops/create-folders.png)

> [!TIP]
> 建立您自己的工作流程時，建議您使程式碼具有等冪性。 例如，使用 [`.create-merge table`](kusto/management/create-merge-table-command.md) 取代 [`.create table`](kusto/management/create-table-command.md) ，並使用函式 [`.create-or-alter`](kusto/management/create-alter-function.md) 而不是 [`.create`](kusto/management/create-function.md) 函數。

## <a name="create-a-release-pipeline"></a>建立發行管線

1. 登入您的 [Azure DevOps 組織](https://dev.azure.com/)。
1. 從左側功能表中選取 [**管線**  >  **發行**]，然後選取 [**新增管線**]。

    ![新增管線](media/devops/new-pipeline.png)

1. [ **新增發行管線** ] 視窗隨即開啟。 在 [ **管線** ] 索引標籤的 [ **選取範本** ] 窗格中，選取 [ **空白作業**]。

     ![選取範本](media/devops/select-template.png)

1. 選取 [ **階段** ] 按鈕。 在 [ **階段** ] 窗格中，新增 **階段名稱**。 選取 [ **儲存** ] 以儲存您的管線。

    ![命名階段](media/devops/stage-name.png)

1. 選取 [ **新增** 成品] 按鈕。 在 [ **新增** 成品] 窗格中，選取您的程式碼所在的存放庫，填寫相關資訊，然後按一下 [ **新增**]。 選取 [ **儲存** ] 以儲存您的管線。

    ![新增構件](media/devops/add-artifact.png)

1. 在 [ **變數** ] 索引標籤中，選取 [ **+ 新增** ]，為將在工作中使用的 **端點 URL** 建立變數。 寫入端點的 **名稱** 和 **值** 。 選取 [ **儲存** ] 以儲存您的管線。 

    ![建立變數](media/devops/create-variable.png)

    若要尋找您的 Endpoint_URL，Azure 入口網站中 **azure 資料總管** 叢集的 [總覽] 頁面包含 azure 資料總管叢集 URI。 以下列格式來建立 URI `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>` 。  例如，HTTPs： \/ /kustodocs.westus.kusto.windows.net？ DatabaseName = SampleDB

    ![Azure 資料總管叢集 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>建立要部署的工作

1. 在 [ **管線** ] 索引標籤中，按一下 [ **1 個作業，0個工作** ] 以新增工作。 

    ![新增工作](media/devops/add-task.png)

1. 依此順序建立三個 **工作** 來部署 **資料表**、函式和 **原則**。 

1. **在 [工作**] 索引標籤中，選取 [ **+** 依 **代理程式作業**]。 搜尋 **Azure 資料總管**。 在 **Marketplace** 中，安裝 **Azure 資料總管-Admin 命令** 延伸模組。 然後，選取 [**執行 Azure 資料總管命令** 中的 [**新增**]。

     ![新增系統管理員命令](media/devops/add-admin-commands.png)

1. 按一下左邊的 [ **Kusto] 命令** ，並以下列資訊更新工作：
    * **顯示名稱**：工作的名稱
    * 檔案 **路徑**：在 [**資料表]** 工作中，指定 [ */Tables/*]，因為資料表建立檔案位於 [*資料表*] 資料夾中。
    * **端點 URL**：輸入 `EndPoint URL` 在上一個步驟中建立的變數。
    * 選取 [ **使用服務端點** ]，然後選取 [ **+ 新增**]。

    ![更新 Kusto 命令工作](media/devops/kusto-command-task.png)

1. 在 [ **新增 Azure 資料總管服務連接** ] 視窗中完成下列資訊：

    |設定  |建議的值  |
    |---------|---------|
    |**連線名稱**     |    輸入名稱以識別此服務端點     |
    |**叢集 Url**    |    您可以在 Azure 資料總管叢集的 [總覽] 區段中找到值，Azure 入口網站 | 
    |**服務主體識別碼**    |    輸入 (建立為必要條件) 的 AAD 應用程式識別碼     |
    |**服務主體應用程式金鑰**     |    輸入 (建立為必要條件) 的 AAD 應用程式金鑰    |
    |**AAD 租使用者識別碼**    |      輸入您的 AAD 租使用者 (，例如 microsoft.com、contoso.com ... )     |

    選取 [ **允許所有管線使用此連接** ] 核取方塊。 選取 [確定]。

    ![新增服務連接](media/devops/add-service-connection.png)

1. 重複步驟1-5 兩 *次，以從 [函* 式] 和 [ *原則* ] 資料夾部署檔案。 選取 [儲存]。 **在 [工作]** 索引標籤中，查看建立的三個工作：**部署資料表**、**部署函數** 和 **部署原則**。

    ![部署所有資料夾](media/devops/deploy-all-folders.png)

1. 選取 [ **+ 發行**  >  **建立版本**] 以建立發行。

    ![建立發行](media/devops/create-release.png)

1. 在 [ **記錄** 檔] 索引標籤中，檢查部署狀態為 [成功]。

    ![部署成功](media/devops/deployment-successful.png)

您現在已完成建立發行管線，以將三個工作部署到進入生產階段前。