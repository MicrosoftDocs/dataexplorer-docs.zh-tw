---
title: 使用 Azure Notebooks 來分析 Azure 資料總管中的資料
description: 本主題說明如何使用 Azure 筆記本建立查詢
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: 4cb0e319cafbd1c59680c7bccc35e2d865d29d40
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942194"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>使用 Azure Notebooks 來分析 Azure 資料總管中的資料

[Azure Notebooks](https://notebooks.azure.com/) 是一項 Azure 雲端服務，可簡化 [Jupyter 筆記本](https://jupyter.org/)的建立和共用。 Azure Notebooks 可讓您輕鬆地結合檔、程式碼和執行程式碼的結果。

> [!Note]
> * 下列程式會使用 Azure Notebooks 環境中的 Python 用戶端來查詢 Azure 資料總管。 不過，您也可以使用 [KQLmagic](kqlmagic.md) 來查詢 Azure 資料總管。
> * 某些使用者報告了使用 Edge 進行驗證的問題;在解決這類問題之前，請使用不同的瀏覽器。

## <a name="create-a-project"></a>建立專案

1. 在瀏覽器中開啟 [Azure Notebooks](https://notebooks.azure.com/) 並登入。

1. 選取標頭中的 [ **我的專案** ] 索引標籤。 

    :::image type="content" source="media/azurenotebooks/an-myprojects.png" alt-text="[專案] 頁面、[我的專案] 索引標籤、Microsoft Azure Notebooks、Azure 資料總管" lightbox="media/azurenotebooks/an-myprojects.png#lightbox":::

1. 選取 [ **+ 新增專案**]。
    
1. 在 [ **建立新專案** ] 對話方塊中：
    1. 輸入 **專案名稱**。
    1. 清除 [ **公用** ] 核取方塊。
        >[!Important]
        > 如果您未清除 [公用] 核取方塊，您的專案就會在開啟的網際網路上公開。
    1. 選取 [建立]****。
    
    ![建立新專案](media/azurenotebooks/an-create-new-project-blank.png)

    系統會自動建立專案識別碼。

## <a name="create-a-notebook"></a>建立 Notebook

1. 在您的新專案中建立筆記本。 筆記本應使用支援的 [語言](https://github.com/Azure/azure-kusto-python#minimum-requirements)。
若要建立筆記本，請選取 [ **+ 新增** ]，然後選取 [ **筆記本**]。

    :::image type="content" source="media/azurenotebooks/an-create-new-notebook-menu.png" alt-text="[專案] 頁面、[我的專案] 索引標籤、Microsoft Azure Notebooks、Azure 資料總管" border="false":::

1. 在 [ **建立新的筆記本** ] 對話方塊中，輸入筆記本名稱。

1. 選取 [ **Python 3.6** ]，然後選取 [ **新增**]。
    
    :::image type="content" source="media/azurenotebooks/an-create-new-notebook.png" alt-text="[專案] 頁面、[我的專案] 索引標籤、Microsoft Azure Notebooks、Azure 資料總管" border="false"::: 
    
1. 在您的專案中，選取新的筆記本。

    ![選取新的筆記本](media/azurenotebooks/an-select-notebook.png)

    等候您的 Python 核心初始化。 當填滿圓形圖示變成空心圓形圖示時，您可以繼續。

    ![核心初始化](media/azurenotebooks/an-python-init-icon.png)

1. 若要匯入系統模組，請輸入下列命令，然後選取 [ **執行**]：
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > 若要執行儲存格，您也可以按下 Shift + Enter。

1.  若要匯入 SDK 類別，請輸入下列命令，然後選取 [ **執行**]：
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  若要建立連接字串並啟動 Kusto 用戶端，請輸入下列命令，然後選取 [ **執行**]：  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. 在提示字元中，開啟新的瀏覽器索引標籤 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 。 
   
1. 輸入授權碼，並登入您的 AAD (@microsoft.com) 帳戶。 Kusto 用戶端 `kc` 現在可以使用您的身分識別向 Azure 資料總管進行驗證。

1. 返回您的筆記本以查看驗證的結果。 

:::image type="content" source="media/azurenotebooks/an-python-commands.png" alt-text="[專案] 頁面、[我的專案] 索引標籤、Microsoft Azure Notebooks、Azure 資料總管" lightbox="media/azurenotebooks/an-python-commands.png#lightbox":::

## <a name="execute-a-kusto-query"></a>執行 Kusto 查詢

1. 輸入您的 Kusto 查詢，然後選取 [ **執行**]。 例如：

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

:::image type="content" source="media/azurenotebooks/an-commands.png" alt-text="[專案] 頁面、[我的專案] 索引標籤、Microsoft Azure Notebooks、Azure 資料總管" lightbox="media/azurenotebooks/an-commands.png#lightbox":::

## <a name="next-steps"></a>後續步驟

* [Kusto-python GitHub 存放庫](https://github.com/Azure/azure-kusto-python)
