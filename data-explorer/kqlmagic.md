---
title: 使用 Jupyter Notebook 來分析 Azure 資料總管中的資料
description: 本主題說明如何使用 Jupyter Notebook 和 kqlmagic 擴充功能來分析 Azure 資料總管中的資料。
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 3af348677bf520d1ccd78388bb6a7a30506e572d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249991"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>使用 Jupyter Notebook 和 kqlmagic 擴充功能來分析 Azure 中的資料資料總管

Jupyter Notebook 是開放原始碼的 Web 應用程式，可讓您建立及共用含有即時程式碼、方程式、視覺效果和敘述文字的文件。 使用方式包含資料清理和轉換、數字模擬、統計模型、資料視覺效果和機器學習。
[Jupyter Notebook](https://jupyter.org/) 支援 magic 函式，可藉由支援其他命令來擴展核心的功能。 kqlmagic 是一種命令，可在 Jupyter Notebook 中擴充 Python 核心的功能，讓您能夠以原生方式執行 Kusto 語言查詢。 您可以輕鬆地結合 Python 和 Kusto 查詢語言，以使用與命令整合的豐富 Plot.ly 程式庫來查詢資料並將其視覺化 `render` 。 支援用於執行查詢的資料來源。 這些資料來源包括 Azure 資料總管、適用于記錄和遙測資料的快速且可高度調整的資料探索服務，以及 Azure 監視器記錄和 Application Insights。 kqlmagic 也可搭配 Azure Data Studio、Jupyter Lab 和 Visual Studio Code Jupyter 延伸模組使用。

## <a name="prerequisites"></a>先決條件

- 屬於 Azure Active Directory (Azure AD) 成員的組織電子郵件帳戶。
- Jupyter Notebook 安裝在本機電腦上或使用 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks/notebooks-kqlmagic?view=sql-server-ver15)

## <a name="install-kqlmagic-library"></a>安裝 kqlmagic 程式庫

1. 安裝 kqlmagic：

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```

1. Load kqlmagic：

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > 按一下核心 > 變更核心 > Python 3.6，以將核心版本變更為 Python 3。6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>連線至 Azure 資料總管協助叢集

使用下列命令來連線至裝載於「協助」** 叢集的「範例」** 資料庫。 若為非 Microsoft Azure AD 使用者，請將租使用者名稱取代為 `Microsoft.com` 您的 Azure AD 租使用者。

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

> [!Note]
> 如果您使用自己的 ADX 叢集，則必須在連接字串中包含該區域，如下所示：   
   ```%kql azuredataexplorer://tenant="youecompany.com";code;cluster='mycluster.westus';database='mykustodb'```

## <a name="query-and-visualize"></a>查詢並以視覺方式呈現

使用 [render 運算子](kusto/query/renderoperator.md)查詢資料，並使用 ploy.ly 程式庫將資料視覺化。 此查詢與視覺效果會提供使用原生 KQL 的整合式體驗。 Kqlmagic 支援大部分的圖表，但 `timepivot`、`pivotchart` 和 `ladderchart` 除外。 所有屬性皆支援轉譯，但 `kind`、`ysplit` 和 `accumulate` 除外。 

### <a name="query-and-render-piechart"></a>查詢和轉譯圓形圖

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>查詢和轉譯時間圖

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> 這些圖表具有互動功能。 選取時間範圍即可放大特定時間。

### <a name="customize-the-chart-colors"></a>自訂圖表色彩

如果您不喜歡預設的色彩選擇區，請使用 [調色板] 選項自訂圖表。 您可以在這裡找到可用的調色板：[選擇 kqlmagic 查詢圖表結果的色彩選擇](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)區

1. 獲取調色盤清單：

    ```python
    %kql --palettes -popup_window
    ```

1. 選取 `cool` 調色盤，然後重新轉譯查詢：

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>使用 Python 將查詢參數化

Kqlmagic 可讓您簡單地在 Kusto 查詢語言與 Python 之間進行交換。 若要深入瞭解：[使用 Python 將 kqlmagic 查詢參數](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)化

### <a name="use-a-python-variable-in-your-kql-query"></a>在 KQL 查詢中使用 Python 變數

您可以在查詢中使用 Python 變數的值來篩選資料：

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>將查詢結果轉換為 Pandas 資料框架

您可以在 Pandas 資料框架中存取 KQL 查詢的結果。 依變數 `_kql_raw_result_` 存取最後一次執行查詢的結果，並輕鬆地將結果轉換為 Pandas 資料框架，如下所示：

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>範例

在許多分析案例中，您可能會想要建立包含許多查詢的可重複使用 Notebook，並將某個查詢的結果饋送給後續查詢。 下列範例會使用 Python 變數 `statefilter` 來篩選資料。

1. 執行查詢來檢視具有最大 `DamageProperty` 的前 10 個狀態：

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. 執行查詢來擷取排行最前的狀態，並將它設定到 Python 變數中：

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. 使用 `let` 陳述式與 Python 變數來執行查詢：

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. 執行 help 命令：

    ```python
    %kql --help "help"
    ```

> [!TIP]
> 若要接收所有可用設定的相關資訊，請使用 `%config Kqlmagic` 。 若要疑難排解和捕獲 Kusto 錯誤（例如連接問題和不正確的查詢），請使用 `%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>後續步驟

執行 help 命令來瀏覽下列包含所有支援功能的範例 Notebook：
- [開始使用適用于 Azure 資料總管的 kqlmagic](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [開始使用 kqlmagic 進行 Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [開始使用 kqlmagic 進行 Azure 監視器記錄](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [使用 Python 參數化您的 kqlmagic 查詢](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [選擇 kqlmagic 查詢圖表結果的色彩選擇區](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
