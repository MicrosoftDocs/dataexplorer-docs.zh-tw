---
title: R 外掛程式 (預覽) -Azure 資料總管
description: 本文描述 Azure 資料總管中 (預覽版) 的 R 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ae8a82253dfd17b7d3667cd4c72648df0c42658a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242791"
---
# <a name="r-plugin-preview"></a>R 外掛程式 (預覽) 

::: zone pivot="azuredataexplorer"

R 外掛程式會使用 R 腳本 (UDF) 執行使用者定義函數。 腳本會取得表格式資料做為輸入，並產生表格式輸出。
外掛程式的執行時間裝載于叢集節點上的 [沙箱](../concepts/sandboxes.md) 中。 沙箱提供隔離且安全的環境。

## <a name="syntax"></a>語法

*T* `|` `evaluate` [ `hint.distribution` `=` (`single`  |  `per_node`) ] `r(` *output_schema* `,` *腳本*[ `,` *script_parameters*]`)`

## <a name="arguments"></a>引數

* *output_schema*： `type` 定義 R 程式碼所傳回之表格式資料輸出架構的常值。
    * 格式為： `typeof(` *ColumnName* `:` *ColumnType*[，...] `)` ，例如： `typeof(col1:string, col2:long)` 。
    * 若要擴充輸入架構，請使用下列語法： `typeof(*, col1:string, col2:long)` 。
* *腳本*： `string` 常值，這是要執行的有效 R 腳本。
* *script_parameters*：選擇性 `dynamic` 常值，這是要傳遞至 R 腳本做為保留字典之名稱和值組的屬性包 `kargs` 。 如需詳細資訊，請參閱 [保留的 R 變數](#reserved-r-variables)。
* *提示。散發*：選擇性的提示，讓外掛程式的執行分散到多個叢集節點上。
   預設：`single`。
    * `single`：腳本的單一實例會對整個查詢資料執行。
    * `per_node`：如果在 R 區塊之前的查詢是散發的，則腳本的實例會在每個節點上執行它所包含的資料。

## <a name="reserved-r-variables"></a>保留的 R 變數

下列變數會保留給 Kusto 查詢語言和 R 程式碼之間的互動：

* `df`：輸入表格式資料 (上述) 的值 `T` ，作為 R 資料框架。
* `kargs`：做為 R 字典的 *script_parameters* 引數值。
* `result`： R 腳本所建立的 R 資料框架。 此值會變成傳送至外掛程式之後任何 Kusto 查詢運算子的表格式資料。

## <a name="enable-the-plugin"></a>啟用外掛程式

* 預設會停用外掛程式。
* 在叢集的 [設定] 索引標籤中 **，啟用** 或停用 Azure 入口網站中的外掛程式。 如需詳細資訊，請參閱 [在 Azure 資料總管叢集中管理語言延伸模組 (Preview) ](../../language-extensions.md)

## <a name="notes-and-limitations"></a>注意事項和限制

* R 沙箱映射 *以適用于 Windows 的 r 3.4.4*為基礎，並包含來自 [Anaconda r Essentials 套件](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)組合的套件。
* R 沙箱會限制對網路的存取。 R 程式碼無法動態安裝映射中未包含的其他套件。 如果您需要特定的封裝，請在 Azure 入口網站中開啟 **新的支援要求** 。

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

* 將外掛程式的輸入資料集縮減為 (資料行/資料列) 所需的最小數量。
    * 可能的話，請使用 Kusto 查詢語言，在源資料集上使用篩選。
    * 若要在來源資料行的子集上進行計算，請先投影這些資料行，再叫用外掛程式。
* 在 `hint.distribution = per_node` 腳本中的邏輯可散佈時使用。
    * 您也可以使用資料分割 [運算子](partitionoperator.md) 來分割輸入資料集。
* 盡可能使用 Kusto 查詢語言來執行 R 腳本的邏輯。

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
    * `'`在 Kusto 查詢中，針對 Kusto 字串常值使用單引號字元 () 。
    * `"`在 r 腳本中使用雙引號字元 (r 字串常值) 。
* 使用 [外部資料運算子](externaldata-operator.md) 來取得您儲存在外部位置的腳本內容，例如 Azure blob 儲存體或公用 GitHub 儲存機制。
  
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

