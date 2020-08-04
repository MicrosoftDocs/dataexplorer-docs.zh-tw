---
title: Python 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Python 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 98888ddd5dd6155c9476163337e7c031e0f84a1e
ms.sourcegitcommit: afc369ab4c4bcc74f2dce22b397a340572db8ecf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528141"
---
# <a name="python-plugin"></a>Python 外掛程式

::: zone pivot="azuredataexplorer"

Python 外掛程式會使用 Python 腳本來執行使用者定義函數（UDF）。 Python 腳本會取得表格式資料做為其輸入，而且預期會產生表格式輸出。
外掛程式的執行時間裝載于[沙箱](../concepts/sandboxes.md)中，並在叢集的節點上執行。

## <a name="syntax"></a>Syntax

*T* `|` `evaluate` [ `hint.distribution` `=` （ `single`  |  `per_node` ）] `python(` *output_schema* `,` *腳本*[ `,` *script_parameters*] [ `,` *external_artifacts*]`)`

## <a name="arguments"></a>引數

* *output_schema*： `type` 定義 Python 程式碼所傳回之表格式資料輸出架構的常值。
    * 格式為： `typeof(` *ColumnName* `:` *ColumnType*[，...] `)` 。例如， `typeof(col1:string, col2:long)` 。
    * 若要擴充輸入架構，請使用下列語法：`typeof(*, col1:string, col2:long)`
* *腳本*： `string` 常值，這是要執行的有效 Python 腳本。
* *script_parameters*：選擇性的 `dynamic` 常值。 這是一組名稱/值組的屬性包，會傳遞至 Python 腳本做為保留的 `kargs` 字典。 如需詳細資訊，請參閱[保留的 Python 變數](#reserved-python-variables)。
* *提示。散發*：要在多個叢集節點間散發之外掛程式執行的選擇性提示。
  * 預設值是 `single`。
  * `single`：腳本的單一實例將會在整個查詢資料上執行。
  * `per_node`：如果散發 Python 區塊之前的查詢，腳本的實例將會在每個節點上，于其包含的資料上執行。
* *external_artifacts*：選擇性的 `dynamic` 常值，這是一組名稱和 URL 組的屬性包，適用于可從雲端儲存體存取的成品。 它們可以讓腳本在執行時間使用。
  * 此屬性包中所參考的 Url 必須是：
    * 包含在叢集的[注解原則](../management/calloutpolicy.md)中。
    * 在公開可用的位置，或提供必要的認證，如[儲存體連接字串](../api/connection-strings/storage.md)中所述。
  * 這些成品可讓腳本從本機臨時目錄取用 `.\Temp` 。 屬性包中提供的名稱會用來做為本機檔案名。 請參閱[範例](#examples)。
  * 如需詳細資訊，請參閱[安裝 Python 外掛程式的套件](#install-packages-for-the-python-plugin)。 

## <a name="reserved-python-variables"></a>保留的 Python 變數

下列變數是保留來進行 Kusto 查詢語言與 Python 程式碼之間的互動。

* `df`：輸入表格式資料（上述的值 `T` ）做為 `pandas` 資料框架。
* `kargs`： *Script_parameters*引數的值，做為 Python 字典。
* `result`： `pandas` Python 腳本所建立的資料框架，其值會變成傳送到外掛程式後面的 Kusto 查詢運算子的表格式資料。

## <a name="enable-the-plugin"></a>啟用外掛程式

* 預設會停用此外掛程式。
* 若要啟用外掛程式，請參閱[必要條件](../concepts/sandboxes.md#prerequisites)清單。
* 在您叢集的 [設定[]](../../language-extensions.md)索引標籤中，啟用或停用 Azure 入口網站中的外掛程式。

## <a name="python-sandbox-image"></a>Python 沙箱映射

* Python 沙箱映射是以*python 3.6*引擎的*Anaconda 5.2.0*散發為基礎。
  請參閱[Anaconda 套件](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)的清單。
  
  > [!NOTE]
  > 少數的封裝可能與執行外掛程式的沙箱所強制執行的限制不相容。
  
* Python 映射也包含一般 ML 套件： `tensorflow` 、 `keras` 、、 `torch` `hdbscan` 、 `xgboost` 和其他有用的套件。
* 外掛程式預設會將*numpy* （as）匯入 `np` & *pandas* （as `pd` ）。  您可以視需要匯入其他模組。

## <a name="use-ingestion-from-query-and-update-policy"></a>使用來自查詢和更新原則的內嵌

* 在下列查詢中使用外掛程式：
  * 定義為[更新原則](../management/updatepolicy.md)的一部分，其來源資料表會內嵌為使用*非串流*內嵌。
  * 執行為[從查詢內嵌之](../management/data-ingestion/ingest-from-query.md)命令的一部分，例如 `.set-or-append` 。
    在這兩種情況下，請確認內嵌的數量和頻率，以及 Python 邏輯所使用的複雜性和資源，與[沙箱限制](../concepts/sandboxes.md#limitations)和叢集的可用資源一致。 如果無法這樣做，可能會導致[節流錯誤](../concepts/sandboxes.md#errors)。
* 您無法在定義為更新原則之一部分的查詢中使用此外掛程式，其來源資料表是使用[串流](../../ingest-data-streaming.md)內嵌進行內嵌。

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

:::image type="content" source="images/plugin/sine-demo.png" alt-text="正弦示範" border="false":::

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

* 將外掛程式的輸入資料集減少為所需的最小數量（資料行/資料列）。
    * 在可能的情況下，使用 Kusto 的查詢語言來篩選源資料集。
    * 若要在來源資料行的子集上進行計算，請先投影那些資料行，再叫用外掛程式。
* `hint.distribution = per_node`每當腳本中的邏輯可散發時使用。
    * 您也可以使用[partition 運算子](partitionoperator.md)來分割輸入資料集。
* 盡可能使用 Kusto 的查詢語言，以執行 Python 腳本的邏輯。

    ## <a name="example"></a>範例

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

* 若要在中產生包含 Python 腳本的多行字串 `Kusto.Explorer` ，請從您最愛的 python 編輯器（*Jupyter*、 *Visual Studio Code*、 *PyCharm*等）複製 Python 腳本。 
  現在，請執行下列其中一個動作：
    * 按**F2**以開啟 [*在 Python 中編輯*] 視窗。 將腳本貼入此視窗。 選取 [確定]。 腳本會以引號和新行裝飾，因此它在 Kusto 中是有效的，而且會自動貼入 [查詢] 索引標籤中。
    * 將 Python 程式碼直接貼入 [查詢] 索引標籤。選取這些線條，然後按**ctrl + K**、 **ctrl + S**快速鍵，以如上所述加以裝飾。 若要反轉，請按**ctrl + K**、 **ctrl + M**快速鍵。 請參閱[查詢編輯器快捷方式](../tools/kusto-explorer-shortcuts.md#query-editor)的完整清單。
* 若要避免 Kusto 字串分隔符號與 Python 字串常值之間的衝突，請使用：
     * `'`Kusto 查詢中 Kusto 字串常值的單引號字元（）
     * `"`Python 腳本中 python 字串常值的雙引號字元（）
* 使用[ `externaldata` 運算子](externaldata-operator.md)來取得您儲存在外部位置（例如 Azure Blob 儲存體）的腳本內容。
  
    ## <a name="example"></a>範例

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

## <a name="install-packages-for-the-python-plugin"></a>安裝適用于 Python 外掛程式的套件

您可能需要自行安裝套件，原因如下：

* 封裝是私用的，而且是您自己的套件。
* 封裝是公用的，但未包含在外掛程式的基底映射中。

安裝套件，如下所示：

### <a name="prerequisites"></a>先決條件

  1. 建立 blob 容器來裝載封裝，最好是與您的叢集位於相同的位置。 例如， `https://artifcatswestus.blob.core.windows.net/python` 假設您的叢集位於美國西部。
  1. 改變叢集的[標注原則](../management/calloutpolicy.md)，以允許存取該位置。
        * 這種變更需要[AllDatabasesAdmin](../management/access-control/role-based-authorization.md)許可權。

        * 例如，若要允許存取位於的 blob `https://artifcatswestus.blob.core.windows.net/python` ，請執行下列命令：

        ```kusto
        .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
        ```

### <a name="install-packages"></a>安裝套件

1. 若為[PyPi](https://pypi.org/)或其他通道中的公用套件，請下載套件及其相依性。

   * 編譯滾輪（ `*.whl` ）檔案（如有必要）。
   * 從本機 Python 環境中的 cmd 視窗，執行：
    
    ```python
    pip wheel [-w download-dir] package-name.
    ```

1. 建立 zip 檔案，其中包含所需的套件及其相依性。

    * 針對私人封裝：壓縮封裝的資料夾和其相依性的資料夾。
    * 若為公用封裝，請壓縮在上一個步驟中下載的檔案。
    
    > [!NOTE]
    > * 請務必壓縮檔案 `.whl` 本身，而不是其上層資料夾。
    > * 您可以略過 `.whl` 已存在於基底沙箱映射中具有相同版本的封裝檔案。

1. 將壓縮檔案上傳至成品位置中的 blob （來自步驟1）。

1. 呼叫 `python` 外掛程式。
    * 以 `external_artifacts` 名稱的屬性包和 zip 檔案（blob 的 URL）的參考，指定參數。
    * 在您的內嵌 python 程式碼中，從匯入， `Zipackage` `sandbox_utils` 並 `install()` 以 zip 檔案的名稱呼叫其方法。

### <a name="example"></a>範例

安裝產生假資料的[Faker](https://pypi.org/project/Faker/)套件。

```kusto
range ID from 1 to 3 step 1 
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

| ID | 名稱         |
|----|--------------|
|   1| Gary Tapia   |
|   2| Emma Evans   |
|   3| Ashley Bowen |

---

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
