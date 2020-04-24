---
title: Azure 資料資源管理員的 Azure DevOps 任務
description: 在本主題中,您將學習建立發佈導管並部署
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: ced839f671a94744799e8a6cde4dbee2a8f17cc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498132"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure 資料資源管理員的 Azure DevOps 任務

[Azure DevOps 服務](https://azure.microsoft.com/services/devops/)提供開發協作工具,如高性能管道、免費專用 Git 儲存庫、可配置看板以及廣泛的自動和連續測試功能。 [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是一種 Azure DevOps 功能,使您能夠管理 CI/CD,以便使用適用於任何語言、平臺和雲端的高性能管道部署代碼。
[Azure 資料資源管理員 - 管理命令](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX)是 Azure 管道任務,它使您能夠創建發佈管道並將資料庫更改部署到 Azure 資料資源管理器資料庫。 它在[視覺工作室市場](https://marketplace.visualstudio.com/)免費提供。

本文件介紹了有關使用**Azure 數據資源管理員和管理命令**任務將架構更改部署到資料庫的簡單示例。 有關完整的 CI/CD 管道,請參閱[Azure DevOps 文件](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* Azure 資料資源管理器叢集設定:
    * [Azure 資料資源管理器叢集和資料庫](/azure/data-explorer/create-cluster-database-portal)
    * 通過[預先 Azure AD 應用程式建立](kusto/management/access-control/how-to-provision-aad-app.md)Azure 活動目錄 (Azure AD) 應用。
    * 通過[管理 Azure 資料資源管理員資料庫許可權](/azure/data-explorer/manage-database-permissions),在 Azure 資料資源管理器資料庫上授予對 Azure AD 應用的訪問許可權。
* Azure DevOps 設定:
    * [註冊免費組織](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [建立組織](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [在 Azure 開發人員排程中建立項目](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [使用 Git 的代碼](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>建立資料夾

在 Git 儲存函式庫中建立以下範例資料夾(*函數*、*策略*、*表*)。 [在此處](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline)將檔案複製到相應的資料夾中,如下所示並提交更改。 提供範例檔以執行以下工作流。

![建立資料夾](media/devops/create-folders.png)

> [!TIP]
> 在創建自己的工作流時,我們建議您使代碼具有冪等性。 例如,使用[.create-merge 表](kusto/management/create-table-command.md#create-merge-table)而不是[.create 表](kusto/management/create-table-command.md),並使用[.create 或更改](kusto/management/create-alter-function.md)函數而不是[.create](kusto/management/create-function.md)函數。

## <a name="create-a-release-pipeline"></a>建立發行管線

1. 登入 Azure [DevOps 組織](https://dev.azure.com/)。
1. 選擇 **「管道** > **從左側功能表中釋放」** 並選擇 **「新建管道**」。

    ![新增管線](media/devops/new-pipeline.png)

1. 將打開 **「新建」管道**視窗。 在線**線線選項**卡中,在「**選擇範本**」窗格中,選擇 **「空作業**」。

     ![選取範本](media/devops/select-template.png)

1. 選擇 **「階段」** 按鈕。 在 **「階段」** 窗格中, 加入**舞臺名稱**。 選擇 **「儲存」** 以儲存導管。

    ![命名舞臺](media/devops/stage-name.png)

1. 選擇 **「新增專案**」按鈕。 在「**新增專案」** 窗格中,選擇程式碼所在的儲存庫,填寫相關資訊,然後按下「**添加**」。 選擇 **「儲存」** 以儲存導管。

    ![新增構件](media/devops/add-artifact.png)

1. 在 **"變數'** 選項卡中,選擇 **"添加"** 以建立將在任務中使用的**終結點網址**的變數。 編寫終結點**的名稱**與**值**。 選擇 **「儲存」** 以儲存導管。 

    ![建立變數](media/devops/create-variable.png)

    要查找**Endpoint_URL,Azure 資料資源管理器群集**在 Azure 門戶中的概述頁包含 Azure 資料資源管理器群集 URI。 以以下格式`https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`構造URI。  例如,HTH:\//kustodocs.westus.kusto.windows.net?資料庫名稱_範例DB

    ![Azure 資料資源管理器叢集 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>建立要部署的工作

1. 在 **「管道」** 選項卡中,按**一下 1 個作業,0 個任務**以新增任務。 

    ![新增工作](media/devops/add-task.png)

1. 以此順序建立三個工作以部署**表**、**函數**與**政策**。 

1. 在「**工作」** 選項卡**+** 中 , 按 **「代理工作**」 選擇 。 搜尋 **Azure 資料總管**。 在**應用商店**中,安裝**Azure 數據資源管理員和管理員命令**擴展。 然後,選擇 **「在****執行 Azure 資料資源管理員」。命令**中添加。

     ![新增管理員指令](media/devops/add-admin-commands.png)

1. 按下左方的**Kusto 指令**,然後使用以下資訊更新工作:
    * **顯示名稱**:工作的名稱
    * **檔案路徑**:在**表**工作中,指定 */table/*.csl,因為表創建檔案位於*表*資料夾中。
    * **終結點網址**:`EndPoint URL`輸入在上一步中建立的變數。
    * 選擇 **「使用服務終結點**」並選擇 **「新建**」。

    ![更新庫斯塔命令任務](media/devops/kusto-command-task.png)

1. 在 **'新增 Azure 資料資源管理員'服務連線**視窗中完成以下資訊:

    |設定  |建議的值  |
    |---------|---------|
    |**連線名稱**     |    輸入名稱以識別此服務終結點     |
    |**叢集網址**    |    值可在 Azure 門戶中的 Azure 資料資源管理器群集的概述部分中找到 | 
    |**服務主體識別碼**    |    輸入 AAD 應用程式 ID(作為先決條件建立)     |
    |**服務主體應用金鑰**     |    輸入 AAD 應用程式金鑰(作為先決條件建立)    |
    |**AAD 租戶識別碼**    |      輸入您的 AAD 租戶(如microsoft.com、contoso.com...    |

    選擇「**允許所有管道使用此連線**」 選單的選項。 選取 [確定]  。

    ![新增服務連線](media/devops/add-service-connection.png)

1. 重複步驟 1-5 兩次以從*函數*和*策略*資料夾中部署檔。 選取 [儲存]  。 在「**工作」** 選項卡中,請參閱建立的三個工作:**部署表**,**部署函數**與**部署策略**。

    ![部署所有資料夾](media/devops/deploy-all-folders.png)

1. 選擇 **+ 發行** > **建立版本**以建立版本。

    ![建立發行](media/devops/create-release.png)

1. 在 **「日誌」** 選項卡中,檢查部署狀態是否成功。

    ![部署成功](media/devops/deployment-successful.png)

您現已完成發佈管道的創建,用於部署三個任務進行預生產。