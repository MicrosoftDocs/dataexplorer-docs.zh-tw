---
title: 使用 Azure Notebooks 來分析 Azure 中的資料資料總管
description: 本主題說明如何使用 Azure 筆記本建立查詢
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: d9564eec41fd78b3506994da2917f1d8765ee266
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373982"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>使用 Azure Notebooks 來分析 Azure 中的資料資料總管

[Azure Notebooks](https://notebooks.azure.com/)是一種 Azure 雲端服務，可簡化[Jupyter 筆記本](https://jupyter.org/)的建立和共用。 Azure Notebooks 可讓您輕鬆地結合檔、程式碼和執行程式碼的結果。

> [!Note]
> * 下列程式會使用 Azure Notebooks 環境中的 Python 用戶端來查詢 Azure 資料總管。 不過，您也可以使用[KQLmagic](kqlmagic.md)來查詢 Azure 資料總管。
> * 某些使用者回報了使用 Edge 進行驗證的問題;在解決這類問題之前，請使用不同的瀏覽器。

## <a name="create-a-project"></a>建立專案

1. 在您的瀏覽器中開啟[Azure Notebooks](https://notebooks.azure.com/) ，然後登入。

1. 選取標題中的 [**我的專案**] 索引標籤。 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. 選取 [ **+ 新增專案**]。
    
1. 在 [**建立新專案**] 對話方塊中：
    1. 輸入**專案名稱**。
    1. 清除 [**公用**] 核取方塊。
        >[!Important]
        > 如果您未清除 [公用] 核取方塊，您的專案將會在開啟的網際網路上公開。
    1. 選取 [建立]。
    
    ![建立新專案](media/azurenotebooks/an-create-new-project-blank.png)

    專案識別碼會自動建立。

## <a name="create-a-notebook"></a>建立 Notebook

1. 在您的新專案中，建立筆記本。 筆記本應使用支援的[語言](https://github.com/Azure/azure-kusto-python#minimum-requirements)。
若要建立筆記本，請選取 [ **+ 新增**]，然後選取 [**筆記本**]。

    ![建立新的筆記本](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. 在 [**建立新筆記本**] 對話方塊中，輸入 [筆記本名稱]。

1. 選取 [ **Python 3.6** ]，然後選取 [**新增**]。
    
    ![建立新的筆記本](media/azurenotebooks/an-create-new-notebook.png) 
    
1. 在您的專案中，選取新的筆記本。

    ![選取新的筆記本](media/azurenotebooks/an-select-notebook.png)

    等到您的 Python 核心初始化。 當實心圓形圖示變更為空心圓形圖示時，您可以繼續。

    ![核心初始化](media/azurenotebooks/an-python-init-icon.png)

1. 若要匯入系統模組，請輸入下列命令，然後選取 [**執行**]：
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > 若要執行儲存格，您也可以按 Shift + Enter。

1.  若要匯入 SDK 類別，請輸入下列命令，然後選取 [**執行**]：
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  若要建立連接字串並啟動 Kusto 用戶端，請輸入下列命令，然後選取 [**執行**]：  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. 在提示字元中，開啟新的瀏覽器索引標籤 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 。 
   
1. 輸入授權碼，並登入您的 AAD （ @microsoft.com ）帳戶。 Kusto 用戶端 `kc` 現在可以使用您的身分識別向 Azure 資料總管進行驗證。

1. 返回您的筆記本以查看驗證的結果。 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>執行 Kusto 查詢

1. 輸入您的 Kusto 查詢，然後選取 [**執行**]。 例如：

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>後續步驟

* [Kusto-python GitHub 存放庫](https://github.com/Azure/azure-kusto-python)
