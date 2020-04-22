---
title: Python 外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理員中的 Python 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5192474779fb712595ff1c25785892bb543fda84
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765721"
---
# <a name="python-plugin"></a>Python 外掛程式

::: zone pivot="azuredataexplorer"

Python 外掛程式使用 Python 文本執行使用者定義的函數 (UDF)。 Python 文本獲取表格數據作為其輸入,並有望生成表格輸出。
外掛程式的運行時託管在[沙箱](../concepts/sandboxes.md)中,在群集的節點上運行。

## <a name="syntax"></a>語法

*T* `|` `evaluate` `hint.distribution` = `single`( | )] output_schema腳本 [ script_parameters ] external_artifacts * `=` `python(` `per_node` *script* *output_schema* `,` `,` *script_parameters* `,` * *`)`

## <a name="arguments"></a>引數

* *output_schema*`type`: 定義由 Python 代碼傳回的表格資料的輸出架構的文字。
    * 格式為:`typeof(`*欄位*`:`*型態*[, ...`)`例如: `typeof(col1:string, col2:long)`.
    * 要延伸輸入架構,請使用以下語法:`typeof(*, col1:string, col2:long)`
* *文稿*`string`: 要執行的 python 文稿的文字。
* *script_parameters*`dynamic`: 可選文字,它是名稱/值對的屬性包,`kargs`作為保留 字典傳遞給 Python 文稿(請參閱保留 Python[變數](#reserved-python-variables))。
* *提示.分發*:外掛程式的執行的可選提示,用於分佈在多個群集節點上。
  * 預設值是 `single`。
  * `single`:腳本的單個實例將運行在整個查詢數據上。
  * `per_node`:如果分發 Python 塊之前的查詢,則腳本的實例將在每個節點上運行,因為它包含的數據。
* *external_artifacts*`dynamic`: 一個可選的文本,它是名稱的屬性包& URL 對,用於可從雲存儲存取的項目,並且可用於腳本在運行時使用。
  * 此屬性套件中引用的網址需要:
  * 包含在群集的[標註策略](../management/calloutpolicy.md)中。
    2. 處於公共可用位置,或提供必要的憑據,如[儲存連接字串](../api/connection-strings/storage.md)中所述。
  * 工件可用於文稿從本地暫存目錄使用`.\Temp`, 屬性包中提供的名稱用作本地檔名(請參閱下面的[範例](#examples))。
  * 有關詳細資訊,請參閱下面的[附錄](#appendix-installing-packages-for-the-python-plugin)。

## <a name="reserved-python-variables"></a>保留 Python 變數

以下變數保留用於 Kusto 查詢語言與 Python 程式碼之間的互動:

* `df`:輸入表格資料(`T`上述值),`pandas`作為 資料幀。
* `kargs`:script_parameters參數的值,*script_parameters*作為 Python 字典。
* `result`:由`pandas`Python 文本建立的 DataFrame,其值將成為發送到外掛程式後面的 Kusto 查詢運算符的表格資料。

## <a name="onboarding"></a>登入

* 默認情況下,外掛程式已禁用。
* [此處](../concepts/sandboxes.md#prerequisites)列出了啟用外掛程式的先決條件。
* 在[群集的 **「設定」** 選項卡中](https://docs.microsoft.com/azure/data-explorer/language-extensions)啟用或禁用 Azure 入口中的外掛程式。

## <a name="notes-and-limitations"></a>註解及限制

* Python 沙箱映像基於使用*Python 3.6*引擎的*Anaconda 5.2.0*分佈。
  這裡[可以找到其](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)包清單(一小部分包可能與運行外掛程式的沙盒強制執行的限制不相容)。
* Python 映像包含常見的 ML`tensorflow``keras``torch`套件`hdbscan``xgboost`: 、 、 、 、 、 和其他有用的套件。
* 默認情況下,外掛程式輸入*數位*`np`(作為 )&`pd`*熊貓*(作為 )。  您可以根據需要導入其他模組。
* **[從查詢](../management/data-ingestion/ingest-from-query.md)與[更新政策](../management/updatepolicy.md)引入**
  * 可以使用外掛程式查詢, 是:
      1. 定義為更新策略的一部分,其源表被引入到使用*非流*式引入。
      2. 作為從查詢中引入的命令的一部分運行(例如`.set-or-append`)。
  * 在上述兩種情況下,建議驗證引入的體積和頻率,以及 Python 邏輯的複雜性和資源利用率是否與[沙箱限制](../concepts/sandboxes.md#limitations)和群集的可用資源保持一致。
    否則可能會導致[限制錯誤](../concepts/sandboxes.md#errors)。
  * *不可能*在定義為更新策略的一部分的查詢中使用外掛程式,該查詢的源表使用[流式引入](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)引入。

## <a name="examples"></a>範例

```kusto
range x from 1 to 360 step 1
| evaluate python(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result = df\n'                     //  The Python decorated script
'n = df.shape[0]\n'
'g = kargs["gain"]\n'
'f = kargs["cycles"]\n'
'result["fx"] = g * np.sin(df["x"]/n*2*np.pi*f)\n'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```
:::image type="content" source="images/samples/sine-demo.png" alt-text="是子示範":::

```kusto
print "This is an example for using 'external_artifacts'"
| evaluate python(
    typeof(File:string, Size:string),
    "import os\n"
    "result = pd.DataFrame(columns=['File','Size'])\n"
    "sizes = []\n"
    "path = '.\\\\Temp'\n"
    "files = os.listdir(path)\n"
    "result['File']=files\n"
    "for file in files:\n"
    "    sizes.append(os.path.getsize(path + '\\\\' + file))\n"
    "result['Size'] = sizes\n"
    "\n",
    external_artifacts = 
        dynamic({"this_is_my_first_file":"https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r",
                 "this_is_a_script":"https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py"})
)
```

| 檔案                  | 大小 |
|-----------------------|------|
| this_is_a_script      | 120  |
| this_is_my_first_file | 105  |

## <a name="performance-tips"></a>效能秘訣

* 將外掛程式的輸入資料集減少到所需的最小數量(列/行)。
    * 如果可能,請使用庫托查詢語言的源數據集上的篩選器。
    * 要對源列的子集執行計算,在調用外掛程式之前僅投影這些列。
* 每當`hint.distribution = per_node`文本中的邏輯是可分發的時,請使用。
    * 您還可以使用[分區運算子](partitionoperator.md)對輸入數據集進行分區。
* 盡可能使用 Kusto 的查詢語言來實現 Python 文稿的邏輯。

    範例：

    ```kusto    
    .show operations
    | where StartedOn > ago(7d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node python( // Using per_node distribution, as the script's logic allows it
        typeof(*, _2d:double),
        'result = df\n'
        'result["_2d"] = 2 * df["d_seconds"]\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(_2d)
    ```

## <a name="usage-tips"></a>使用提示

* `Kusto.Explorer`要產生包含 Python 文稿的多行字串,請從您最喜愛的 Python 編輯器 *(Jupyter、Visual* *Studio 碼**、PyCharm*等)複製 Python 文稿,然後:
    * 以*F2*開啟**Python 視窗中的編輯**。 將腳本貼上到此視窗中。 選取 [確定]  。 文本將用引號和新行(因此在 Kusto 中有效)進行修飾,並自動貼上到查詢選項卡中。
    * 將 Python 代碼直接貼上到查詢選項卡中,選擇這些行並按*Ctrl+M**Ctrl_K、Ctrl_S*熱鍵以修飾它們(以反轉它按*Ctrl+K**Ctrl_K、Ctrl_M*熱鍵)。 [下面是](../tools/kusto-explorer-shortcuts.md#query-editor)查詢編輯器快捷方式的完整清單。
* 為了避免 Kusto 字串分隔符與 Python 字串文字之間的衝突,我們建議在`'`Kusto 查詢中使用 Kusto 字串文字的單引號字`"`元 ( ), 在 Python 文稿中對 Python 字串文字使用雙引號字元 ( ) 。
* 使用[外部資料運算元](externaldata-operator.md)獲取存儲在外部位置(如 Azure Blob 儲存)的腳本的內容。
  
    **範例**

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate python(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

## <a name="appendix-installing-packages-for-the-python-plugin"></a>附錄:Python 外掛程式安裝套件

由於以下任何原因,您可能需要自行安裝包:

* 包是私人的,是你自己的。
* 該包是公共的,但不包括在外掛程式的基本映射中。

您可以依照以下步驟安裝套件:

1. 一次性先決條件:
  
  a. 創建 blob 容器以承載包,最好與群集位於同一區域。
    * 例如:(https://artifcatswestus.blob.core.windows.net/python假設群集位於美國西部)
  
  b. 更改群集的[標註策略](../management/calloutpolicy.md)以允許訪問該位置。
    * 這需要[所有資料庫管理員](../management/access-control/role-based-authorization.md)許可權。
    * 例如,要啟用對 位於https://artifcatswestus.blob.core.windows.net/python的 blob 的存取,要執行的命令是:

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. 對於公共包(在[PyPi](https://pypi.org/)或其他通道中)a. 下載包及其依賴項。
  b. 如果需要,編譯到輪`*.whl`( ) 檔:
    * 從 cmd 視窗(在本地 Python 環境中)執行:
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. 建立 zip 檔案,其中包含所需的套件及其相依項:

    * 對於公共包:壓縮上一步中下載的檔。
    * 注意：
        * 請確保壓縮檔本身,`.whl`*而不是*其父資料夾。
        * 您可以跳過`.whl`基本沙箱圖像中已存在相同版本的包的檔。
    * 對於私有包:壓縮包的資料夾及其依賴項的資料夾

4. 將壓縮檔上載到工件位置中的 blob(從步驟 1)。

5. 呼叫`python`外掛程式:
    * 使用名稱`external_artifacts`的屬性包和對 zip 檔案(blob 的 URL)的引用指定參數。
    * 在內聯 python`Zipackage`代碼`sandbox_utils`中 :`install()`從 匯入 並呼叫其方法,並帶有 zip 檔的名稱。

### <a name="example"></a>範例

安裝產生虛假資料的[Faker](https://pypi.org/project/Faker/)套件:

```kusto
range Id from 1 to 3 step 1 
| extend Name=''
| evaluate python(typeof(*),
    'from sandbox_utils import Zipackage\n'
    'Zipackage.install("Faker.zip")\n'
    'from faker import Faker\n'
    'fake = Faker()\n'
    'result = df\n'
    'for i in range(df.shape[0]):\n'
    '    result.loc[i, "Name"] = fake.name()\n',
    external_artifacts=pack('faker.zip', 'https://artifacts.blob.core.windows.net/kusto/Faker.zip?...'))
```

| Id | 名稱         |
|----|--------------|
|   1| 加里·塔皮亞   |
|   2| 艾瑪·埃文斯   |
|   3| 阿什利·鮑文 |

---

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
