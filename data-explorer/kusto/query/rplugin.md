---
title: R 外掛程式（預覽）-Azure 資料總管
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
ms.openlocfilehash: 429140a1ae74dc0d7189e4b8dec1a32428fe3bb3
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902148"
---
# <a name="r-plugin-preview"></a>R 外掛程式（預覽）

::: zone pivot="azuredataexplorer"

R 外掛程式會使用 R 腳本來執行使用者定義函數（UDF）。 腳本會取得表格式資料做為其輸入，並產生表格式輸出。
外掛程式的執行時間裝載于叢集節點上的[沙箱](../concepts/sandboxes.md)中。 沙箱提供隔離且安全的環境。

## <a name="syntax"></a>語法

*T* `|` `evaluate` [ `hint.distribution` `=` （ `single`  |  `per_node` ）] `r(` *output_schema* `,` *腳本*[ `,` *script_parameters*]`)`

## <a name="arguments"></a>引數

* *output_schema*： `type` 定義表格式資料之輸出架構的常值（由 R 程式碼傳回）。
    * 格式為： `typeof(` *ColumnName* `:` *ColumnType*[，...] `)` ，例如： `typeof(col1:string, col2:long)` 。
    * 若要擴充輸入架構，請使用下列語法： `typeof(*, col1:string, col2:long)` 。
* *腳本*： `string` 常值，這是要執行的有效 R 腳本。
* *script_parameters*：選擇性的 `dynamic` 常值，這是一組名稱和值組的屬性包，會傳遞至 R 腳本做為保留的 `kargs` 字典。 如需詳細資訊，請參閱[保留的 R 變數](#reserved-r-variables)。
* *提示。散發*：要在多個叢集節點間散發之外掛程式執行的選擇性提示。
   預設：`single`。
    * `single`：腳本的單一實例將會在整個查詢資料上執行。
    * `per_node`：如果散發 R 區塊之前的查詢，腳本的實例將會透過它所包含的資料在每個節點上執行。

## <a name="reserved-r-variables"></a>保留的 R 變數

下列變數是保留來進行 Kusto 查詢語言與 R 程式碼之間的互動：

* `df`：輸入表格式資料（上述的值 `T` ），做為 R 資料框架。
* `kargs`： *Script_parameters*引數的值，做為 R 字典。
* `result`： R 腳本所建立的 R 資料框架。 值會變成表格式資料，會傳送至緊接在外掛程式後面的任何 Kusto 查詢運算子。

## <a name="enable-the-plugin"></a>啟用外掛程式

* 預設會停用此外掛程式。
* 在叢集的 [設定] 索引標籤中 **，啟用**或停用 Azure 入口網站中的外掛程式。 如需詳細資訊，請參閱[在 Azure 資料總管叢集中管理語言擴充功能（預覽）](../../language-extensions.md)

## <a name="notes-and-limitations"></a>注意事項和限制

* R 沙箱映射是*以適用于 Windows 的 r 3.4.4 為*基礎，並包含來自[Anaconda 的 r Essentials](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)組合的套件。
* R 沙箱會限制對網路的存取。 R 程式碼無法動態安裝不包含在映射中的其他套件。 如果您需要特定的套件，請在 Azure 入口網站中開啟**新的支援要求**。

## <a name="examples"></a>範例

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

## <a name="performance-tips"></a>效能秘訣

* 將外掛程式的輸入資料集減少為所需的最小數量（資料行/資料列）。
    * 可能的話，請使用 Kusto 查詢語言，在源資料集上使用篩選。
    * 若要在來源資料行的子集上進行計算，請在叫用外掛程式之前只投影那些資料行。
* `hint.distribution = per_node`每當腳本中的邏輯可散發時使用。
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

## <a name="usage-tips"></a>使用提示

* 若要避免 Kusto 字串分隔符號和 R 字串分隔符號之間發生衝突：  
    * `'`在 Kusto 查詢中使用單引號字元（）來 Kusto 字串常值。
    * `"`在 r 腳本中使用雙引號字元（）來表示 r 字串常值。
* 使用[外部資料運算子](externaldata-operator.md)來取得您儲存在外部位置（例如 Azure blob 儲存體或公用 GitHub 存放庫）的腳本內容。
  
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

Azure 監視器不支援這項功能

::: zone-end

