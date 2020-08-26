---
title: 使用 VS Code 來 Debug Kusto 查詢語言內嵌 Python-Azure 資料總管
description: 瞭解如何使用 VS Code， (KQL) 內嵌 Python 來將 Kusto 查詢語言進行調試。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: 21e3da862931219a338d2364117b8990280118d9
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874285"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>使用 VS Code Debug Kusto 查詢語言內嵌 Python

Azure 資料總管支援使用 [python ( # A1 外掛程式](kusto/query/pythonplugin.md)，以 Kusto 查詢語言執行內嵌的 python 程式碼。 外掛程式執行時間裝載于沙箱中，也就是隔離且安全的 Python 環境。 Python ( # A1 外掛程式功能可將 Kusto 的查詢語言原生功能延伸至 OSS Python 套件的大量保存。 此延伸模組可讓您在查詢中執行先進的演算法，例如機器學習、人工智慧、統計和時間序列。

Kusto 查詢語言工具不方便開發和偵測 Python 演算法。 因此，在您最愛的 Python 整合式開發環境（例如 Jupyter、PyCharm、VS 或 VS Code）上開發演算法。 當演算法完成時，請複製並貼到 KQL 中。 為了改善並簡化此工作流程，Azure 資料總管支援 Kusto Explorer 或 Web UI 用戶端之間的整合 VS Code，以及用於撰寫和 KQL 內嵌 Python 程式碼的。 

> [!NOTE]
> 此工作流程只能用來偵測相對較小的輸入資料表， (最多) 個 MB。 因此，您可能需要限制輸入以進行調試。  如果您需要處理大型資料表，請將它限制為使用 `| take` 、 `| sample` 或進行調試 `where rand() < 0.x` 。

## <a name="prerequisites"></a>先決條件

1. 安裝 Python [Anaconda 散發](https://www.anaconda.com/distribution/#download-section)套件。 在 [ **Advanced Options**] 中，選取 [ **將 Anaconda 新增至我的 PATH 環境變數**]。
2. 安裝 [Visual Studio Code](https://code.visualstudio.com/Download)
3. 安裝 [適用于 Visual Studio Code 的 Python 擴充](https://marketplace.visualstudio.com/items?itemName=ms-python.python)功能。

## <a name="run-your-query-in-your-client-application"></a>在用戶端應用程式中執行您的查詢

1. 在您的用戶端應用程式中，為包含內嵌 Python 的查詢加上前置詞 `set query_python_debug;`
1. 執行查詢。
    * Kusto Explorer：系統會使用 *debug_python .py* 腳本自動啟動 VS Code。
    * Kusto Web UI： 
        1. 下載並儲存 *debug_python .py*、 *df.txt*和 *kargs.txt*。 在視窗中，選取 [ **允許**]。 **將檔案儲存** 在選取的目錄中。 

            ![Web UI 會下載內嵌的 python 檔案](media/debug-inline-python/webui-inline-python.png)

        1. 在 [.py] 上 *debug_python* 按一下滑鼠右鍵，然後使用 VS code 開啟。 
        .Py 腳本包含內嵌的 Python 程式碼，從 KQL 查詢中，以範本程式碼作為前置詞，以從*df.txt*和*kargs.txt*的參數字典初始化輸入資料框架。 *debug_python*    
            
1. 在 vs code 中，啟動 vs code 偵錯工具： **Debug**  >  **開始偵錯工具 (F5) **，然後選取 [ **Python**設定]。 偵錯工具將會啟動並自動中斷點來偵測內嵌程式碼。

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS Code 中的內嵌 Python 調試作業如何運作？

1. 查詢會在伺服器中進行剖析和執行，直到達到必要的 `| evaluate python()` 子句為止。
1. 將會叫用 Python 沙箱，但不會執行程式碼，而是序列化輸入資料表、參數的字典和程式碼，然後將其傳回給用戶端。
1. 這三個物件會儲存在下列三個檔案中： *df.txt*、 *kargs.txt*和 *debug_python.* 在選取的目錄 (Web UI) 或用戶端% TEMP% 目錄 (Kusto Explorer) 中 .py。
1. VS code 會啟動，並預先載入 *debug_python .py* 檔案，其中包含前置詞程式碼，可從其個別的檔案中初始化 df 和 kargs，後面接著內嵌于 KQL 查詢中的 python 腳本。

## <a name="query-example"></a>查詢範例

1. 在您的用戶端應用程式中執行下列 KQL 查詢：

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    請參閱產生的表格：

    | x  | 四  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. 使用下列程式，在您的用戶端應用程式中執行相同的 KQL 查詢 `set query_python_debug;` ：

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code 已啟動：

    ![啟動 VS code](media/debug-inline-python/launch-vs-code.png)

1. VS Code 在 debug 主控台中進行調試和列印「結果」資料框架：

    ![VS code 偵錯工具](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python 沙箱映射和您的本機安裝之間可能會有差異。 藉[由查詢外掛程式來檢查特定封裝的沙箱映射](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)。
