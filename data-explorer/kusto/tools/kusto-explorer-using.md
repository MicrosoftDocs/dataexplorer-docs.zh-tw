---
title: 使用 Kusto.Explorer
description: 瞭解如何使用 Kusto
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 601a2b90b3a9152df701f001f050ab0c48e8910d
ms.sourcegitcommit: 6e84f50efc8c5c3fe57080341ed3effe72197886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87440037"
---
# <a name="using-kustoexplorer"></a>使用 Kusto.Explorer

Kusto 是一種桌面應用程式，可讓您在便於使用的使用者介面中使用 Kusto 查詢語言來流覽資料。 本文說明如何使用搜尋和查詢模式、共用查詢，以及管理叢集、資料庫和資料表。

## <a name="search-mode"></a>搜尋 + + 模式

搜尋 + + 模式可讓您在一或多個資料表中使用搜尋語法來搜尋詞彙。

1. 在 [首頁] 索引標籤的 [查詢] 下拉式清單中，選取 [**搜尋 + +**]。
1. 選取**多個資料表**。
1. 在 **[選擇資料表]** 下，定義要搜尋的資料表。
1. 在 [編輯] 方塊中，輸入您的搜尋片語，然後選取 [ **Go**]。
1. [資料表/時間位置] 方格的熱度圖會顯示出現的字詞和位置。

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus.png" alt-text="搜尋 + + Kusto Explorer":::

1. 選取方格中的資料格，然後選取 [ **View Details** ]，以在 [結果] 窗格中顯示相關專案。

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus-results.png" alt-text="Kusto Explorer 搜尋 + + 結果":::

## <a name="query-mode"></a>查詢模式

Kusto 包含功能強大的腳本模式，可讓您撰寫、編輯和執行特定查詢。 腳本模式隨附語法醒目提示和 IntelliSense，因此您可以快速提升 Kusto 查詢語言的知識。

本節說明如何在 Kusto 中執行基本查詢，以及如何將參數新增至您的查詢。

## <a name="basic-queries"></a>基本查詢

如果您有資料表記錄，可以開始探索它們：

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
StormEvents | count 
```

當您的游標位於這一行時，它會彩色灰色。 按**F5**執行查詢。 

以下是一些更多範例查詢：

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

:::image type="content" source="images/kusto-explorer-using/basic-query.png" alt-text="Kusto Explorer 基本查詢":::

深入瞭解[Kusto 查詢語言](https://docs.microsoft.com/azure/kusto/query/)。

> [!NOTE]
> 查詢運算式中的空白行可能會影響執行查詢的哪個部分。
>
> 如果未選取任何文字，則會假設查詢或命令以空白行分隔。
> 如果選取文字，則會執行選取的文字。

## <a name="client-side-query-parameterization"></a>用戶端查詢參數化

> [!NOTE]
> Kusto 中有兩種類型的查詢 parametrization 技術：
> * [語言整合式查詢 parametrization](../query/queryparametersstatement.md)會實作為查詢引擎的一部分，並可供以程式設計方式查詢服務的應用程式使用。 本檔未說明這個方法。
>
> * 用戶端查詢 parametrization （如下所述）只是 Kusto 應用程式的一項功能。 這相當於在傳送這些查詢以由服務執行之前，先在查詢上使用字串取代作業。 下面所述的語法不是查詢語言本身的一部分，而且無法在傳送查詢至服務時使用，方法是 Kusto。

如果您在多個查詢或多個索引標籤中使用相同的值，在每個使用的位置變更該值非常不方便。 這就是為什麼 Kusto 支援查詢參數的原因。 查詢參數會在索引標籤之間共用，因此可以輕鬆地重複使用。 參數是以 {} 括弧表示。 例如： `{parameter1}`

腳本編輯器會反白顯示查詢參數：

:::image type="content" source="images/kusto-explorer-using/parametrized-query-1.png" alt-text="參數化查詢1":::

您可以輕鬆地定義和編輯現有的查詢參數：


:::image type="content" source="images/kusto-explorer-using/parametrized-query-2.png" alt-text="編輯參數化查詢2":::


:::image type="content" source="images/kusto-explorer-using/parametrized-query-3.png" alt-text="編輯參數化查詢3":::

腳本編輯器也具有 IntelliSense，適用于已定義的查詢參數：

:::image type="content" source="images/kusto-explorer-using/parametrized-query-4.png" alt-text="Paramaterized 查詢 IntelliSense":::

您可以有多個參數集（列在 [**參數集**] 下拉式方塊中）。
選取 [**加入新**的] 或 [**刪除目前**的] 以指令引數集清單。

:::image type="content" source="images/kusto-explorer-using/parametrized-query-5.png" alt-text="參數集清單":::

## <a name="share-queries-and-results"></a>共用查詢和結果

在 Kusto 中，您可以透過電子郵件來共用查詢和結果。 您也可以建立會在瀏覽器中開啟並執行查詢的深層連結。

### <a name="share-queries-and-results-by-email"></a>透過電子郵件共用查詢和結果

Kusto 提供一個便利的方式，讓您透過電子郵件共用查詢和查詢結果。

1. 在 Kusto 中[執行查詢](#basic-queries)。
1. 在 [首頁] 索引標籤的 [共用] 區段中，選取 [**匯出至剪貼**簿] \ （或按 Ctrl + Shift + C）。

    :::image type="content" source="images/kusto-explorer-using/menu-export.png" alt-text="匯出至剪貼簿":::

    Kusto 會將下列內容貼入剪貼簿：
     * 您的查詢
     * 查詢結果（資料表或圖表）
     * Kusto 叢集和資料庫的連線詳細資料
     * 會自動重新執行查詢的連結

1. 將剪貼簿的內容貼入新的電子郵件訊息。

    :::image type="content" source="images/kusto-explorer-using/share-results-2.png" alt-text="在電子郵件中共用結果":::

### <a name="deep-linking-queries"></a>深層連結查詢

您可以建立在瀏覽器中開啟的 URI，在本機開啟 Kusto，並在指定的 Kusto 資料庫上執行特定查詢。

### <a name="limitations"></a>限制

查詢限制為 ~ 2000 個字元，因為瀏覽器限制、HTTP proxy，以及用來驗證連結的工具（例如 Microsoft Outlook）。 其限制是因為它相依于叢集和資料庫名稱長度。 如需詳細資訊，請參閱 [https://support.microsoft.com/kb/208427](https://support.microsoft.com/kb/208427)。 若要減少達到字元數限制的機率，請參閱下方的[取得較短的連結](#getting-shorter-links)。

URI 的格式為：`https://<ClusterCname>.kusto.windows.net/<DatabaseName>web=0?query=<QueryToExecute>`

例如：[https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10](https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10)
 
此 URI 會開啟 Kusto，連接到 `Help` Kusto 叢集，並在資料庫上執行指定的查詢 `Samples` 。 如果有 Kusto 實例已在執行中，則執行中的實例將會開啟新的索引標籤，並在其中執行查詢。

> [!NOTE] 
> 基於安全性理由，已停用控制命令的深層連結。

### <a name="creating-a-deep-link"></a>建立深層連結

建立深層連結最簡單的方式，就是在 Kusto 中撰寫查詢，然後使用將 `Export to Clipboard` 查詢（包括深層連結和結果）複製到剪貼簿。 然後您可以透過電子郵件來共用它。
        
複製到電子郵件時，深層連結會以小型字型顯示。 例如：

https://help.kusto.windows.net:443/Samples[[按一下以執行查詢](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

第一個連結會開啟 Kusto，並適當地設定叢集和資料庫內容。
第二個連結（ `Click to run query` ）是深層連結。 如果您將連結移至電子郵件訊息，然後按 CTRL + K，您就可以看到實際的 URL：

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>深層連結和參數化查詢

您可以使用參數化查詢搭配深層連結。

1. 建立查詢以形成參數化查詢（例如， `KustoLogs | where Timestamp > ago({Period}) | count` ） 
1. 為 URI 中的每個查詢參數提供參數，例如： 
    
    `https://<your_cluster>.kusto.windows.net/MyDatabase?
web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

    &lt; &gt; 以您的 Azure 資料總管叢集名稱取代 your_cluster。


### <a name="getting-shorter-links"></a>取得較短的連結

查詢可能會變長。 若要減少查詢超過最大長度的機率，請使用 `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` Kusto 用戶端程式庫中提供的方法。 這個方法會產生更精簡的查詢版本。 Kusto 也能辨識較短的格式。

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

藉由套用下一個轉換，讓查詢變得更精簡：

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto. Explorer 命令列引數

命令列引數是用來設定工具，以便在啟動時執行其他功能。 例如，載入腳本並連接到叢集。 因此，命令列引數不是任何 Kusto. Explorer 功能的取代。

命令列引數會當做用來開啟應用程式之 URL 的一部分來傳遞，其方式類似于[查詢深層連結](#creating-a-deep-link)。

## <a name="command-line-argument-syntax"></a>命令列引數語法

Kusto 支援下列語法中的數個命令列引數（順序很重要）：

[*LocalScriptFile*][*QueryString*]

* *LocalScriptFile*是您本機電腦上的指令檔名，必須要有副檔名 `.kql` 。 如果這類檔案存在，Kusto 會在啟動時自動載入此檔案。
* *QueryString*是使用 HTTP 查詢字串格式的字串。 這個方法會提供其他屬性，如下表所述。

例如，若要以名為的腳本檔案啟動 Kusto， `c:\temp\script.kql` 並設定為與叢集 `help` 、資料庫通訊 `Samples` ，請使用下列命令：

```kusto
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|引數  |描述                                                               |
|----------|--------------------------------------------------------------------------|
|**要執行的查詢**                                                                 |
|`query`   |要執行的查詢（base64 編碼）。 如果空白，請使用 `querysrc` 。          |
|`querysrc`|保存要執行之查詢的檔案或 blob 的 URL （如果 `query` 是空的）。|
|**連接到 Kusto 叢集**                                                  |
|`uri`     |要連接之 Kusto 叢集的連接字串。                 |
|`name`    |連接至 Kusto 叢集的顯示名稱。                  |
|**連接群組**                                                                 |
|`path`    |要下載之連接群組檔案的 URL （URL 編碼）。             |
|`group`   |連接群組的名稱。                                         |
|`filename`|保留連接群組的本機檔案。                              |


## <a name="manage-clusters-databases-tables-or-function-authorized-principals"></a>管理叢集、資料庫、資料表或功能授權的主體

> [!NOTE]
> 只有系統[管理員](../management/access-control/role-based-authorization.md)可以在自己的範圍中新增或卸載授權的主體。

以滑鼠右鍵按一下 [連線][面板](kusto-explorer.md#connections-tab)中的目標實體，然後選取 [**管理叢集授權的主體**]。 （您也可以從 [管理] 功能表中選取此選項）。

:::image type="content" source="images/kusto-explorer-using/right-click-manage-authorized-principals.png" alt-text="管理授權的主體":::

:::image type="content" source="images/kusto-explorer-using/manage-authorized-principals-window.png" alt-text="管理授權的主體視窗":::

* 若要新增已授權的主體，請選取 [**新增主體**]，提供主體詳細資料，然後確認動作。
    
    :::image type="content" source="images/kusto-explorer-using/add-authorized-principals-window.png" alt-text="新增授權的主體":::

    :::image type="content" source="images/kusto-explorer-using/confirm-add-authorized-principals.png" alt-text="確認新增授權的主體":::

* 若要卸載現有的授權主體，請選取 [**捨棄主體**] 並確認動作。

    :::image type="content" source="images/kusto-explorer-using/confirm-drop-authorized-principals.png" alt-text="確認捨棄已授權的主體":::


## <a name="next-steps"></a>後續步驟

* [Kusto. Explorer 鍵盤快速鍵](kusto-explorer-shortcuts.md)
* [Kusto.Explorer 選項](kusto-explorer-options.md)
* [針對 Kusto 進行疑難排解](kusto-explorer-troubleshooting.md)

深入瞭解 Kusto. Explorer 工具和公用程式：
* [Kusto. Explorer 程式碼分析器](kusto-explorer-code-analyzer.md)
* [Kusto. Explorer 程式碼導覽](kusto-explorer-codenav.md)
* [Kusto 程式碼重構](kusto-explorer-refactor.md)
* [Kusto 查詢語言 (KQL)](https://docs.microsoft.com/azure/kusto/query/)
