---
title: 使用 Azure 筆記型分析 Azure 資料資源管理員中的數據
description: 本主題示範如何使用 Azure 筆記本建立查詢
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496260"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>使用 Azure 筆記型分析 Azure 資料資源管理員中的數據

[Azure 筆記本](https://notebooks.azure.com/)是一種 Azure 雲服務,它簡化了創建和共用[Jupyter 筆記本](https://jupyter.org/)。 Azure 筆記本使組合文檔、代碼和運行代碼的結果變得容易。

> [!Note]
> * 以下過程使用 Azure 筆記本環境中的 Python 客戶端來查詢 Azure 資料資源管理器。 但是,您也可以使用[KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic)查詢 Azure 資料資源管理器。
> * 某些使用者報告使用 Edge 進行身份驗證的問題;在此類問題得到解決之前,請使用其他瀏覽器。

## <a name="create-a-project"></a>建立專案

1. 在瀏覽器中打開[Azure 筆記本](https://notebooks.azure.com/)並登錄。

1. 在標題中選擇 **「我的項目**」選項卡。 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. 選擇 **= 新的項目**。
    
1. 在 **「建立新項目」** 對話框中:
    1. 輸入**項目名稱**。
    1. 清除 **"公共**"複選框。
        >[!Important]
        > 如果不清除"公共"複選框,您的專案將在打開的 Internet 上公開。
    1. 選取 [建立]  。
    
    ![建立新專案](media/azurenotebooks/an-create-new-project-blank.png)

    項目 ID 將自動建立。

## <a name="create-a-notebook"></a>建立 Notebook

1. 在新專案中,創建筆記本。 筆記本應使用[受支援的語言](https://github.com/Azure/azure-kusto-python#minimum-requirements)。
要創建筆記本,請選擇 **"新建"** 並選擇 **"筆記本**"。

    ![建立新筆記本](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. 在"**創建新筆記本"** 對話框中,輸入筆記本名稱。

1. 選擇**Python 3.6**並選擇 **"新建**" 。
    
    ![建立新筆記本](media/azurenotebooks/an-create-new-notebook.png) 
    
1. 在專案中,選擇新筆記本。

    ![選擇新筆記本](media/azurenotebooks/an-select-notebook.png)

    等待,直到 Python 內核初始化。 當填充的圓圖示更改為空心圓圖示時,您可以繼續。

    ![內核初始化](media/azurenotebooks/an-python-init-icon.png)

1. 要匯入系統模組,請輸入以下指令並選擇 **「執行**:
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > 要運行單元格,還可以按 Shift_Enter。

1.  要匯入 SDK 類別,請輸入以下指令並選擇 **「執行 :**
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  要產生連接字串並啟動 Kusto 客戶端,請輸入以下指令並選擇 **「執行**:  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. 在提示符下,打開新的瀏覽器選項卡。 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 
   
1. 輸入授權代碼,並登錄您的 AAD@microsoft.com( ) 帳戶。 Kusto`kc`客戶端 現在可以使用您的標識向 Azure 資料資源管理員進行身份驗證。

1. 返回到筆記本以查看身份驗證結果。 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>執行庫斯托查詢

1. 輸入庫斯托查詢並選擇 **「運行**」。 例如：

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>後續步驟

* [庫斯圖-python GitHub 儲存庫](https://github.com/Azure/azure-kusto-python)
