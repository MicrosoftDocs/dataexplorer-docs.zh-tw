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
ms.openlocfilehash: 918d0f2f7fa8667a4cf90b2813bb3b80dd49fa78
ms.sourcegitcommit: 335e05864e18616c10881db4ef232b9cda285d6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97596850"
---
# <a name="python-plugin"></a>Python 外掛程式

::: zone pivot="azuredataexplorer"

Python 外掛程式會使用 Python 腳本來執行使用者定義函數 (UDF) 。 Python 腳本會取得表格式資料做為輸入，並預期會產生表格式輸出。
外掛程式的執行時間裝載于 [沙箱](../concepts/sandboxes.md)中，並在叢集的節點上執行。

## <a name="syntax"></a>語法

*T* `|` `evaluate` [ `hint.distribution` `=` (`single`  |  `per_node`) ] `python(` *output_schema* `,` *腳本*[ `,` *script_parameters*] [ `,` *external_artifacts*]`)`

## <a name="arguments"></a>引數

* *output_schema*： `type` 定義 Python 程式碼所傳回之表格式資料輸出架構的常值。
    * 格式為： `typeof(` *ColumnName* `:` *ColumnType*[，...] `)` 。例如， `typeof(col1:string, col2:long)` 。
    * 若要擴充輸入架構，請使用下列語法： `typeof(*, col1:string, col2:long)`
* *腳本*： `string` 常值，這是要執行的有效 Python 腳本。
* *script_parameters*：選擇性 `dynamic` 常值。 它是一組名稱/值組的屬性包，會以保留字典的形式傳遞至 Python 腳本 `kargs` 。 如需詳細資訊，請參閱 [保留的 Python 變數](#reserved-python-variables)。
* *提示。散發*：選擇性的提示，讓外掛程式的執行分散到多個叢集節點上。
  * 預設值是 `single`。
  * `single`：腳本的單一實例會對整個查詢資料執行。
  * `per_node`：如果在 Python 區塊之前的查詢已散發，則腳本的實例會在每個節點上執行，並在其所包含的資料上執行。
* *external_artifacts*：選擇性的 `dynamic` 常值，這是可從雲端儲存體存取之成品的名稱和 URL 配對的屬性包。 它們可供腳本在執行時間使用。
  * 這個屬性包中所參考的 Url 必須是：
    * 包含在叢集中的 [標注原則](../management/calloutpolicy.md)中。
    * 在公開可用的位置，或提供必要的認證，如 [儲存體連接字串](../api/connection-strings/storage.md)中所述。
  * 成品可供腳本從本機臨時目錄使用 `.\Temp` 。 屬性包中提供的名稱是用來做為本機檔案名。 請參閱 [範例](#examples)。
  * 如需詳細資訊，請參閱 [安裝適用于 Python 外掛程式的套件](#install-packages-for-the-python-plugin)。 

## <a name="reserved-python-variables"></a>保留的 Python 變數

下列變數會保留給 Kusto 查詢語言與 Python 程式碼之間的互動。

* `df`：輸入表格式資料 (上述) 的值 `T` 做為 `pandas` 資料框架。
* `kargs`：做為 Python 字典的 *script_parameters* 引數值。
* `result`： `pandas` Python 腳本所建立的資料框架，其值會變成傳送至外掛程式後面之 Kusto 查詢運算子的表格式資料。

## <a name="enable-the-plugin"></a>啟用外掛程式

* 預設會停用外掛程式。
* 若要啟用外掛程式，請參閱 [必要條件](../concepts/sandboxes.md#prerequisites)清單。
* 在叢集的 [設定 []](../../language-extensions.md)索引標籤中，啟用或停用 Azure 入口網站中的外掛程式。

## <a name="python-sandbox-image"></a>Python 沙箱映射

* Python 沙箱映射是以 *python 3.6* 引擎的 *Anaconda 5.2.0* 散發為基礎。
  查看 [Anaconda 套件](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/)的清單。
  
  > [!NOTE]
  > 較小百分比的封裝可能與執行外掛程式的沙箱強制執行的限制不相容。
  
* Python 映射也包含常見的 ML 套件： `tensorflow` 、 `keras` 、 `torch` 、 `hdbscan` 、 `xgboost` 和其他有用的封裝。
* 根據預設，外掛程式會將 *numpy* (`np`) & *pandas* (為 `pd`) 。  您可以視需要匯入其他模組。

## <a name="use-ingestion-from-query-and-update-policy"></a>使用來自查詢和更新原則的內嵌

* 在下列查詢中使用外掛程式：
  * 定義為 [更新原則](../management/updatepolicy.md)的一部分，其來源資料表會內嵌為使用 *非串流* 內嵌。
  * 作為 [從查詢內嵌之](../management/data-ingestion/ingest-from-query.md)命令的一部分來執行，例如 `.set-or-append` 。
    在這兩種情況下，請確認內嵌的數量和頻率，以及 Python 邏輯所使用的複雜性和資源，與 [沙箱限制](../concepts/sandboxes.md#limitations) 和叢集的可用資源相符。 如果無法這麼做，可能會導致 [節流錯誤](../concepts/sandboxes.md#errors)。
* 您無法在定義為更新原則一部分的查詢中使用此外掛程式，其來源資料表是使用 [串流](../../ingest-data-streaming.md)內嵌來內嵌的。

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

* 將外掛程式的輸入資料集縮減為 (資料行/資料列) 所需的最小數量。
    * 可能的話，請使用 Kusto 的查詢語言，在源資料集上使用篩選。
    * 若要在來源資料行的子集上進行計算，請先投影這些資料行，再叫用外掛程式。
* 在 `hint.distribution = per_node` 腳本中的邏輯可散佈時使用。
    * 您也可以使用資料分割 [運算子](partitionoperator.md) 來分割輸入資料集。
* 盡可能使用 Kusto 的查詢語言，以執行 Python 腳本的邏輯。

    ### <a name="example"></a>範例

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

* 若要在中產生包含 Python 腳本的多行字串 `Kusto.Explorer` ，請從您最愛的 python 編輯器複製您的 python 腳本 (*Jupyter*、 *Visual Studio Code*、 *PyCharm* 等) 。 
  現在請執行下列其中一項：
    * 按 **F2** 以開啟 [ *在 Python 中編輯* ] 視窗。 將腳本貼入此視窗。 選取 [確定]。 腳本將以引號和新行裝飾，因此它在 Kusto 中是有效的，而且會自動貼入 [查詢] 索引標籤中。
    * 將 Python 程式碼直接貼到 [查詢] 索引標籤中。選取這些行，然後按 **ctrl + K**、 **ctrl + S** 快速鍵，以如上所述裝飾它們。 若要反轉，請按 **ctrl + K**、 **ctrl + M** 熱鍵。 請參閱 [查詢編輯器快速鍵](../tools/kusto-explorer-shortcuts.md#query-editor)的完整清單。
* 若要避免 Kusto 字串分隔符號和 Python 字串常值之間發生衝突，請使用：
     * `'`Kusto 查詢中 Kusto 字串常值的單引號字元 () 
     * `"`Python 腳本中 python 字串常值 () 雙引號字元
* 使用[ `externaldata` 運算子](externaldata-operator.md)來取得您儲存在外部位置（例如 Azure Blob 儲存體）的腳本內容。
  
    ### <a name="example"></a>範例

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

您可能會因為下列原因而需要自行安裝套件 (s) ：

* 封裝是私用的，而且是您自己的套件。
* 封裝是公用的，但不包含在外掛程式的基底映射中。

安裝套件，如下所示：

### <a name="prerequisites"></a>Prerequisites

  1. 建立 blob 容器來裝載套件，最好是在與叢集相同的位置。 例如， `https://artifcatswestus.blob.core.windows.net/python` 假設您的叢集位於美國西部。
  1. 改變叢集的注標 [原則](../management/calloutpolicy.md) ，以允許存取該位置。
        * 這種變更需要 [AllDatabasesAdmin](../management/access-control/role-based-authorization.md) 許可權。

        * 例如，若要允許存取位於中的 blob `https://artifcatswestus.blob.core.windows.net/python` ，請執行下列命令：

        ```kusto
        .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
        ```

### <a name="install-packages"></a>安裝套件

1. 針對 [PyPi](https://pypi.org/) 或其他通道中的公用封裝，下載封裝及其相依性。

   * `*.whl`如有需要， () 檔編譯滾輪。
   * 從本機 Python 環境的 cmd 視窗中，執行：
    
    ```python
    pip wheel [-w download-dir] package-name.
    ```

1. 建立 zip 檔案，其中包含必要的封裝及其相依性。

    * 若為私用套件：壓縮封裝的資料夾及其相依性的資料夾。
    * 針對公用封裝，壓縮上一個步驟中所下載的檔案。
    
    > [!NOTE]
    > * 請務必壓縮檔案 `.whl` 本身，而不是其父資料夾。
    > * 您可以 `.whl` 針對已存在於基底沙箱映射中相同版本的套件，略過檔案。

1. 將壓縮的檔案上傳至步驟 1)  (構件位置中的 blob。

1. 呼叫 `python` 外掛程式。
    * `external_artifacts`使用 (BLOB URL) 之 zip 檔案的屬性包來指定參數。
    * 在內嵌 python 程式碼中，從匯入， `Zipackage` `sandbox_utils` 並使用 zip 檔案的名稱呼叫其 `install()` 方法。

### <a name="example"></a>範例

安裝產生假資料的 [Faker](https://pypi.org/project/Faker/) 封裝。

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
