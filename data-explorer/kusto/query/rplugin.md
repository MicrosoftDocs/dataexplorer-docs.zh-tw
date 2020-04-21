---
title: R 外掛程式 (預覽) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 R 外掛程式(預覽)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 06655cc260f23b8812450513f85bec63c33f1775
ms.sourcegitcommit: 857e7062c00a3ccff3c7085375d5c077936afaa5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524407"
---
# <a name="r-plugin-preview"></a>R 外掛程式 (預覽版)

::: zone pivot="azuredataexplorer"

R 外掛程式使用 R 文稿執行使用者定義的函數 (UDF)。 R 文本獲取表格數據作為其輸入,並有望生成表格輸出。
外掛程式的運行時託管在[沙箱](../concepts/sandboxes.md)中,沙箱是一個在群集節點上運行的隔離和安全的環境。

### <a name="syntax"></a>語法

*T* `|` *script_parameters* `evaluate` `per_node` =`hint.distribution` ( | ) ] output_schema腳本 [ script_parameters ] `=` `single` `r(` *output_schema* `,` *script* `,``)`


### <a name="arguments"></a>引數

* *output_schema*`type`: 定義表格資料的輸出架構的文本,由 R 代碼傳回。
    * 格式為:`typeof(`*欄位*`:`*型態*[, ...`)`例如: `typeof(col1:string, col2:long)`.
    * 要延伸輸入架構,請使用以下語法: `typeof(*, col1:string, col2:long)`。
* *文稿*`string`: 要執行的有效 R 文稿的文字。
* *script_parameters*`dynamic`: 可選文字,它是名稱/值對的屬性包,`kargs`作為保留 字典傳遞給 R 腳本(請參閱保留 R[變數](#reserved-r-variables))。
* *提示.分發*:外掛程式的執行的可選提示,用於分佈在多個群集節點上。
   預設：`single`。
    * `single`:腳本的單個實例將運行在整個查詢數據上。
    * `per_node`:如果分發 R 塊之前的查詢,則腳本的實例將在每個節點上運行,因為它包含的數據。


### <a name="reserved-r-variables"></a>保留 R 變數

以下變數保留用於 Kusto 查詢語言與 R 程式碼之間的互動:

* `df`:輸入表格資料(`T`上述值),作為 R 資料幀。
* `kargs`:script_parameters參數的值,*script_parameters*作為 R 字典。
* `result`:由 R 文稿建立的 R DataFrame,其值將成為發送到外掛程式後面的任何 Kusto 查詢運算符的表格資料。

### <a name="onboarding"></a>登入


* 默認情況下,外掛程式已禁用。
    * *有興趣在群集上啟用外掛程式?*
        
        * 在 Azure 門戶中,在 Azure 資料資源管理器群集中,在左側功能表中選擇 **「新建支援請求**」。
        * 禁用外掛程式還需要打開支援票證。

### <a name="notes-and-limitations"></a>註解及限制

* R 沙箱影像基於 Windows*的 R 3.4.4*,包括[Anaconda R 套件中的套件](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)。
* R 沙箱限制訪問網路,因此 R 代碼無法動態安裝映射中未包括的其他包。如果需要特定包,請在 Azure 門戶中打開 **"新支援請求**"。


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
![alt 文字](./images/samples/sine-demo.png "正在展示")




### <a name="performance-tips"></a>效能秘訣

* 將外掛程式的輸入資料集減少到所需的最小數量(列/行)。
    * 如果可能,請使用庫斯托查詢語言對源數據集使用篩選器。
    * 要對源列的子集執行計算,在調用外掛程式之前僅投影這些列。
* 每當`hint.distribution = per_node`文本中的邏輯是可分發的時,請使用。
    * 您還可以使用[分區運算子](partitionoperator.md)對輸入數據集進行分區。
* 只要有可能,請使用 Kusto 查詢語言來實現 R 文稿的邏輯。

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

* 為了避免 Kusto 字串分隔符與 R 的分隔元之間的衝突,我們建議在`'`Kusto 查詢中使用 Kusto 字串文字的單引號`"`字元 ( ), 在 R 文稿中的 R 字串文字使用雙引號字元 ( ) 。
* 使用[外部資料運算元](externaldata-operator.md)獲取存儲在外部位置的腳本的內容,例如 Azure Blob 儲存、公共 GitHub 儲存庫等。
  
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

