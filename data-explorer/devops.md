---
title: 適用于 Azure 資料總管的 Azure DevOps 工作
description: 在本主題中，您將瞭解如何建立發行管線並部署
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 7d0c45a587a396da934bcb3465d60e101d0150a0
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967157"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>適用于 Azure 資料總管的 Azure DevOps 工作

[Azure DevOps Services](https://azure.microsoft.com/services/devops/)提供開發共同作業工具，例如高效能管線、免費私人 Git 存放庫、可設定的看板面板，以及廣泛的自動化和持續測試功能。 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是一項 Azure DevOps 的功能，可讓您管理 CI/CD 以使用適用于任何語言、平臺和雲端的高效能管線來部署您的程式碼。
[Azure 資料總管-系統管理命令](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX)是 Azure Pipelines 的工作，可讓您建立發行管線，並將您的資料庫變更部署至 Azure 資料總管資料庫。 它在[Visual Studio Marketplace](https://marketplace.visualstudio.com/)中免費提供。

本檔描述使用**Azure 資料總管–管理命令**工作將架構變更部署到資料庫的簡單範例。 如需完整的 CI/CD 管線，請參閱[Azure DevOps 檔](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* Azure 資料總管叢集設定：
    * [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
    * 藉由布建[Azure AD 應用程式](kusto/management/access-control/how-to-provision-aad-app.md)來建立 Azure Active Directory （Azure AD）應用程式。
    * 藉由[管理 azure 資料總管資料庫許可權](manage-database-permissions.md)，將存取權授與您的 azure 資料總管資料庫上的 Azure AD App。
* Azure DevOps 安裝程式：
    * [註冊免費的組織](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [建立組織](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [在 Azure DevOps 中建立專案](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [使用 Git 的程式碼](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>建立資料夾

在您的 Git 存放庫中建立下列範例資料夾（*函數*、*原則*、*資料表*）。 將檔案從[這裡](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline)複製到各自的資料夾，如下所示並認可變更。 提供範例檔案來執行下列工作流程。

![建立資料夾](media/devops/create-folders.png)

> [!TIP]
> 當您建立自己的工作流程時，我們建議讓您的程式碼具有等冪性。 例如，請使用[. create-merge table](kusto/management/create-merge-table-command.md) ，而不是[create table](kusto/management/create-table-command.md)，並使用[. create-或-alter](kusto/management/create-alter-function.md) function，而不是[create](kusto/management/create-function.md) function。

## <a name="create-a-release-pipeline"></a>建立發行管線

1. 登入您的[Azure DevOps 組織](https://dev.azure.com/)。
1. 從左側功能表中選取 [**管線**  >  **發行**]，然後選取 [**新增管線**]。

    ![新增管線](media/devops/new-pipeline.png)

1. [**新增發行管線**] 視窗隨即開啟。 在 [**管線**] 索引標籤的 [**選取範本**] 窗格中，選取 [**空白作業**]。

     ![選取範本](media/devops/select-template.png)

1. 選取 [**階段**] 按鈕。 在 [**階段**] 窗格中，新增**階段名稱**。 選取 [**儲存**] 以儲存管線。

    ![為階段命名](media/devops/stage-name.png)

1. 選取 [**新增**成品] 按鈕。 在 [**加入**成品] 窗格中，選取您的程式碼所在的存放庫，並填寫相關資訊，然後按一下 [**新增**]。 選取 [**儲存**] 以儲存管線。

    ![新增構件](media/devops/add-artifact.png)

1. 在 [**變數**] 索引標籤中，選取 [ **+ 新增**] 以建立將在工作中使用之**端點 URL**的變數。 寫入端點的**名稱**和**值**。 選取 [**儲存**] 以儲存管線。 

    ![建立變數](media/devops/create-variable.png)

    若要尋找您的 Endpoint_URL，Azure 入口網站中**azure 資料總管**叢集的 [總覽] 頁面包含 azure 資料總管叢集 URI。 以下列格式來建立 URI `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>` 。  例如，HTTPs： \/ /kustodocs.westus.kusto.windows.net？ DatabaseName = SampleDB

    ![Azure 資料總管叢集 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>建立要部署的工作

1. 在 [**管線**] 索引標籤中，按一下 [ **1 個作業，0個工作**] 以新增工作。 

    ![新增工作](media/devops/add-task.png)

1. 依照此順序，建立三個**工作**來部署**資料表**、函式和**原則**。 

1. **在 [工作**] 索引標籤中，選取 [ **+** 依**Agent 作業**]。 搜尋 **Azure 資料總管**。 在**Marketplace**中，安裝**Azure 資料總管-Admin 命令**延伸模組。 然後，在 [**執行 Azure 資料總管命令**] 中選取 [**新增**]。

     ![新增管理命令](media/devops/add-admin-commands.png)

1. 按一下左側的 [ **Kusto] 命令**，並使用下列資訊來更新工作：
    * **顯示名稱**：工作的名稱
    * 檔案**路徑**：在 [**資料表]** 工作中，指定 [ */Tables/*]，因為資料表建立檔案位於 [*資料表*] 資料夾中。
    * **端點 URL**：輸入 `EndPoint URL` 在上一個步驟中建立的變數。
    * 選取 [**使用服務端點**]，然後選取 [ **+ 新增**]。

    ![更新 Kusto 命令工作](media/devops/kusto-command-task.png)

1. 在 [**新增 Azure 資料總管服務**連線] 視窗中，完成下列資訊：

    |設定  |建議的值  |
    |---------|---------|
    |**連接名稱**     |    輸入名稱以識別此服務端點     |
    |**叢集 Url**    |    您可以在 Azure 資料總管叢集的 [總覽] 區段中找到值，其位於 Azure 入口網站 | 
    |**服務主體識別碼**    |    輸入 AAD 應用程式識別碼（建立為先決條件）     |
    |**服務主體應用程式金鑰**     |    輸入 AAD 應用程式金鑰（建立為先決條件）    |
    |**AAD 租使用者識別碼**    |      輸入您的 AAD 租使用者（例如 microsoft.com，contoso.com ...）    |

    選取 [**允許所有管線使用此連接**] 核取方塊。 選取 [確定]。

    ![新增服務連接](media/devops/add-service-connection.png)

1. 重複步驟1-5 另兩*次，從 [函*式] 和 [*原則*] 資料夾部署檔案。 選取 [儲存]。 在 [**工作]** 索引標籤中，查看建立的三個工作：**部署資料表**、**部署**函式，以及**部署原則**。

    ![部署所有資料夾](media/devops/deploy-all-folders.png)

1. 選取 [ **+ 發行**] [  >  **建立發行**] 來建立發行。

    ![建立發行](media/devops/create-release.png)

1. 在 [**記錄**] 索引標籤中，檢查部署狀態是否為 [成功]。

    ![部署成功](media/devops/deployment-successful.png)

您現在已完成建立發行管線，以將三個工作部署至生產階段前。