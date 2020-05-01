---
title: R 外掛程式（預覽）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 R 外掛程式（預覽）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 514c67133980c9ab1c38b65cc51e4592dcb15eda
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618952"
---
# <a name="r-plugin-preview"></a>R 外掛程式（預覽）

::: zone pivot="azuredataexplorer"

R 外掛程式會使用 R 腳本來執行使用者定義函數（UDF）。 R 腳本會取得表格式資料做為其輸入，而且預期會產生表格式輸出。
外掛程式的執行時間裝載于[沙箱](../concepts/sandboxes.md)中，這是在叢集節點上執行的隔離且安全的環境。

### <a name="syntax"></a>語法

*T* `|` `per_node` `,` *script_parameters* *output_schema* *script* [`hint.distribution` （`single`）] `r(`output_schema`,`腳本 [script_parameters] `evaluate` `=`  | `)`


### <a name="arguments"></a>引數

* *output_schema*：定義`type`表格式資料之輸出架構的常值（由 R 程式碼傳回）。
    * 格式為： `typeof(` *ColumnName* `:` *ColumnType* [，...]`)`，例如： `typeof(col1:string, col2:long)`。
    * 若要擴充輸入架構，請使用下列語法： `typeof(*, col1:string, col2:long)`。
* *腳本*： `string`常值，這是要執行的有效 R 腳本。
* *script_parameters*：選擇性`dynamic`常值，這是一組名稱/值組的屬性包，會傳遞至 R 腳本做為`kargs`保留的字典（請參閱[保留的 R 變數](#reserved-r-variables)）。
* *提示。散發*：要在多個叢集節點間散發之外掛程式執行的選擇性提示。
   預設：`single`。
    * `single`：腳本的單一實例將會在整個查詢資料上執行。
    * `per_node`：如果散發 R 區塊之前的查詢，腳本的實例將會透過它所包含的資料在每個節點上執行。


### <a name="reserved-r-variables"></a>保留的 R 變數

下列變數是保留來進行 Kusto 查詢語言與 R 程式碼之間的互動：

* `df`：輸入表格式資料（ `T`上述的值），做為 R 資料框架。
* `kargs`： *Script_parameters*引數的值，做為 R 字典。
* `result`： R 腳本所建立的 R 資料框架，其值會變成傳送到外掛程式後面任何 Kusto 查詢運算子的表格式資料。

### <a name="onboarding"></a>入門訓練


* 預設會停用此外掛程式。
    * *有興趣在叢集上啟用外掛程式嗎？*
        
        * 在 Azure 入口網站的 Azure 資料總管叢集內，從左側功能表中選取 [**新增支援要求**]。
        * 停用外掛程式也需要開啟支援票證。

### <a name="notes-and-limitations"></a>注意事項和限制

* R 沙箱映射是*以適用于 Windows 的 r 3.4.4 為*基礎，並包含來自[Anaconda 的 r Essentials](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)組合的套件。
* R 沙箱會限制存取網路，因此 R 程式碼無法動態安裝不包含在映射中的其他套件。如果您需要特定的套件，請在 Azure 入口網站中開啟**新的支援要求**。


### <a name="examples"></a>範例

```kusto
range x from 1 to 360 step 1
| evaluate r(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result <- df\n'                    //  The R decorated script
'n <- nrow(df)\n'
'g <- kargs$gain\n'
'f <- kargs$cycles\n'
'result$fx <- g * sin(df$x / n * 2 * pi * f)'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="正弦示範" border="false":::

### <a name="performance-tips"></a>效能秘訣

* 將外掛程式的輸入資料集減少為所需的最小數量（資料行/資料列）。
    * 在可能的情況下，使用 Kusto 查詢語言，在源資料集上使用篩選。
    * 若要在來源資料行的子集上執行計算，請先投影那些資料行，再叫用外掛程式。
* 每當`hint.distribution = per_node`腳本中的邏輯可散發時使用。
    * 您也可以使用[partition 運算子](partitionoperator.md)來分割輸入資料集。
* 如果可能，請使用 Kusto 查詢語言來執行 R 腳本的邏輯。

    例如：

    ```kusto    
    .show operations
    | where StartedOn > ago(1d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node r( // Using per_node distribution, as the script's logic allows it
        typeof(*, d2:double),
        'result <- df\n'
        'result$d2 <- df$d_seconds\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(d2)
    ```

### <a name="usage-tips"></a>使用提示

* 若要避免 Kusto 字串分隔符號與 R 之間發生衝突，建議您針對 Kusto 查詢中的`'`Kusto 字串常值使用單引號字元（），並在 r 腳本`"`中使用雙引號字元（）來括住 r 字串常值。
* 使用[externaldata 運算子](externaldata-operator.md)來取得您儲存在外部位置的腳本內容，例如 Azure blob 儲存體、公用 GitHub 儲存機制等等。
  
  例如：

    ```kusto    
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate r(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

---

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end

