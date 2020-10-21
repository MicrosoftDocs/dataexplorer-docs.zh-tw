---
title: 使用 Kusto.Explorer
description: 瞭解如何使用 Kusto
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 17623f739c3bc3a8573d208434753b879931ac02
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342768"
---
# <a name="using-kustoexplorer"></a>使用 Kusto.Explorer

Kusto 是桌面應用程式，可讓您在簡單易用的使用者介面中使用 Kusto 查詢語言來探索您的資料。 本文說明如何使用搜尋和查詢模式、共用您的查詢，以及管理叢集、資料庫和資料表。

## <a name="search-mode"></a>搜尋 + + 模式

[搜尋 + +] 模式可讓您在一或多個資料表中使用搜尋語法來搜尋詞彙。

1. 在 [首頁] 索引標籤的 [查詢] 下拉式清單中，選取 [ **搜尋 + +**]。
1. 選取 **多個資料表**。
1. 在 **[選擇資料表]** 下，定義要搜尋的資料表。
1. 在編輯方塊中，輸入您的搜尋片語，然後選取 [ **移至**]。
1. [表格/時間位置] 方格的熱度圖會顯示哪些詞彙出現，以及顯示的位置。

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus.png" alt-text="搜尋 + + Kusto Explorer":::

1. 選取方格中的資料格，然後選取 [ **視圖詳細資料** ]，即可在 [結果] 窗格中顯示相關的專案。

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus-results.png" alt-text="搜尋 + + Kusto Explorer":::

## <a name="query-mode"></a>查詢模式

Kusto 包含強大的腳本模式，可讓您撰寫、編輯和執行臨機操作查詢。 腳本模式隨附語法醒目提示和 IntelliSense，讓您可以快速提升 Kusto 查詢語言的知識。

本節說明如何在 Kusto 中執行基本查詢，以及如何將參數加入至您的查詢。

## <a name="basic-queries"></a>基本查詢

如果您有資料表記錄，可以開始探索它們：

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
StormEvents | count 
```

當游標位於這行時，它會以灰色呈現。 按 **F5** 執行查詢。 

以下是一些查詢範例：

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

:::image type="content" source="images/kusto-explorer-using/basic-query.png" alt-text="搜尋 + + Kusto Explorer":::

深入瞭解 [Kusto 查詢語言](../query/index.md)。

> [!NOTE]
> 查詢運算式中的空白行可能會影響執行查詢的哪個部分。
>
> 如果未選取任何文字，則會假設查詢或命令以空白行分隔。
> 如果選取文字，則會執行選取的文字。

## <a name="client-side-query-parameterization"></a>用戶端查詢參數化

> [!NOTE]
> Kusto 中有兩種類型的查詢 parametrization 技術：
> * [語言整合式查詢 parametrization](../query/queryparametersstatement.md) 會實作為查詢引擎的一部分，而且可供以程式設計方式查詢服務的應用程式使用。 本檔未說明這個方法。
>
> * 以下所述的用戶端查詢 parametrization 只是 Kusto. Explorer 應用程式的一項功能。 這相當於在將查詢傳送給服務執行之前，在查詢上使用字串取代作業。 以下所述的語法不是查詢語言本身的一部分，而且無法在將查詢傳送至服務時，使用 Kusto 的方式來使用。

如果您在多個查詢或多個索引標籤中使用相同的值，則在使用它的每個位置都不太方便變更該值。 這就是為什麼 Kusto 支援查詢參數的原因。 查詢參數會在索引標籤之間共用，讓您可以輕鬆地重複使用它們。 參數會以 {} 括弧表示。 例如：`{parameter1}`

腳本編輯器會反白顯示查詢參數：

:::image type="content" source="images/kusto-explorer-using/parametrized-query-1.png" alt-text="搜尋 + + Kusto Explorer":::

您可以輕鬆地定義和編輯現有的查詢參數：


:::image type="content" source="images/kusto-explorer-using/parametrized-query-2.png" alt-text="搜尋 + + Kusto Explorer":::


:::image type="content" source="images/kusto-explorer-using/parametrized-query-3.png" alt-text="搜尋 + + Kusto Explorer":::

腳本編輯器也有已定義之查詢參數的 IntelliSense：

:::image type="content" source="images/kusto-explorer-using/parametrized-query-4.png" alt-text="搜尋 + + Kusto Explorer":::

您可以有多組參數 (列在 [ **參數設定** ] 下拉式方塊) 中。
選取 [ **加入新** 的或 **刪除目前** 的] 以指令引數集的清單。

:::image type="content" source="images/kusto-explorer-using/parametrized-query-5.png" alt-text="搜尋 + + Kusto Explorer":::

## <a name="share-queries-and-results"></a>共用查詢和結果

在 Kusto 中，您可以透過電子郵件共用查詢和結果。 您也可以建立可在瀏覽器中開啟及執行查詢的深層連結。

### <a name="share-queries-and-results-by-email"></a>以電子郵件共用查詢和結果

Kusto 可讓您輕鬆地透過電子郵件共用查詢和查詢結果。

1. 在 Kusto 中[執行您的查詢](#basic-queries)。
1. 在 [首頁] 索引標籤的 [共用] 區段中，選取 [ **匯出至剪貼** 簿] (或按 Ctrl + Shift + C) 。

    :::image type="content" source="images/kusto-explorer-using/menu-export.png" alt-text="搜尋 + + Kusto Explorer":::

    Kusto 會將下列內容貼到剪貼簿：
     * 您的查詢
     * 查詢結果 (資料表或圖表) 
     * Kusto 叢集和資料庫的連線詳細資料
     * 將會自動重新執行查詢的連結

1. 將剪貼簿的內容貼到新的電子郵件訊息中。

    :::image type="content" source="images/kusto-explorer-using/share-results-2.png" alt-text="搜尋 + + Kusto Explorer":::

### <a name="deep-linking-queries"></a>深層連結查詢

您可以建立一個在瀏覽器中開啟的 URI，在本機開啟 Kusto，並在指定的 Kusto 資料庫上執行特定的查詢。

> [!NOTE] 
> 基於安全性理由，已停用 control 命令的深層連結。

#### <a name="creating-a-deep-link"></a>建立深層連結

建立深層連結最簡單的方式，就是在 Kusto 中撰寫您的查詢，然後用 `Export to Clipboard` 來將查詢 (包括深層連結和結果) 到剪貼簿。 然後您可以透過電子郵件共用。
        
複製到電子郵件時，深層連結會以 small 字型顯示。 例如：

https://help.kusto.windows.net:443/Samples [[按一下以執行查詢](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

第一個連結會開啟 Kusto，並適當地設定叢集和資料庫內容。
第二個連結 (`Click to run query`) 是深層連結。 如果您將連結移至電子郵件訊息，然後按下 CTRL + K，則可以看到實際的 URL：

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

#### <a name="deep-links-and-parametrized-queries"></a>深層連結和參數化查詢

您可以使用參數化查詢搭配深層連結。

1. 建立以參數化查詢形式形成的查詢 (例如， `KustoLogs | where Timestamp > ago({Period}) | count`)  
1. 為 URI 中的每個查詢參數提供參數，例如： 
    
    `https://<your_cluster>.kusto.windows.net/MyDatabase?
web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

    以您的 Azure 資料總管叢集名稱取代 &lt;your_cluster&gt;。

#### <a name="limitations"></a>限制

查詢限制為 ~ 2000 個字元，因為瀏覽器限制、HTTP proxy 以及用來驗證連結的工具，例如 Microsoft Outlook。 限制是大約的，因為它相依于叢集和資料庫名稱長度。 如需詳細資訊，請參閱 [https://support.microsoft.com/kb/208427](https://support.microsoft.com/kb/208427)。 

若要減少達到字元限制的機率，請參閱 [取得較短的連結](#getting-shorter-links)。

URI 的格式為： `https://<ClusterCname>.kusto.windows.net/<DatabaseName>web=0?query=<QueryToExecute>`

例如： [https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10](https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10)
 
此 URI 會開啟 Kusto，並連接到 Kusto 叢集 `Help` ，並在資料庫上執行指定的查詢 `Samples` 。 如果 Kusto 的實例已經在執行中，執行中的實例將會開啟新的索引標籤，並在其中執行查詢。

### <a name="getting-shorter-links"></a>取得較短的連結

查詢可能會很長。 若要減少查詢超過長度上限的機率，請使用 `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` Kusto 用戶端程式庫中提供的方法。 這個方法會產生更精簡的查詢版本。 Kusto 也能辨識較短的格式。

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

藉由套用下一個轉換，讓查詢變得更精簡：

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto. Explorer 命令列引數

命令列引數是用來設定工具，以在啟動時執行其他功能。 例如，載入腳本並連接到叢集。 因此，命令列引數不會取代任何 Kusto. Explorer 功能。

命令列引數會當做用來開啟應用程式之 URL 的一部分傳遞，其方式類似于 [查詢深層連結](#creating-a-deep-link)。

### <a name="command-line-argument-syntax"></a>命令列引數語法

Kusto 支援下列語法中的數個命令列引數， (順序的重要性) ：

[*LocalScriptFile*][*QueryString*]

* *LocalScriptFile* 是您本機電腦上必須有副檔名的指令檔名 `.kql` 。 如果有這類檔案，Kusto 就會在啟動時自動載入此檔案。
* *QueryString* 是使用 HTTP 查詢字串格式的字串。 這個方法會提供其他屬性，如下表所述。

例如，若要使用名為的腳本檔案來啟動 Kusto， `c:\temp\script.kql` 並設定為與叢集 `help` 、資料庫通訊， `Samples` 請使用下列命令：

```kusto
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|引數  |說明                                                               |
|----------|--------------------------------------------------------------------------|
|**要執行的查詢**                                                                 |
|`query`   |要執行 (base64 編碼) 的查詢。 如果是空的，請使用 `querysrc` 。          |
|`querysrc`|保存查詢來執行 (的檔案或 blob 的 URL，如果 `query` 是空的) 。|
|**連接到 Kusto 叢集**                                                  |
|`uri`     |要連接之 Kusto 叢集的連接字串。                 |
|`name`    |連接至 Kusto 叢集的顯示名稱。                  |
|**連接群組**                                                                 |
|`path`    |要下載 (URL 編碼) 的連接群組檔案的 URL。             |
|`group`   |連接群組的名稱。                                         |
|`filename`|保存連接群組的本機檔案。                              |


## <a name="manage-clusters-databases-tables-or-function-authorized-principals"></a>管理叢集、資料庫、資料表或功能授權的主體

> [!NOTE]
> 只有系統 [管理員](../management/access-control/role-based-authorization.md) 可以在自己的範圍中新增或卸載授權的主體。

在 [連線] [面板](kusto-explorer.md#connections-tab)中的目標實體上按一下滑鼠右鍵，然後選取 [ **管理叢集授權的主體**]。  (您也可以從 [管理] 功能表中選取此選項。 ) 

:::image type="content" source="images/kusto-explorer-using/right-click-manage-authorized-principals.png" alt-text="搜尋 + + Kusto Explorer":::

:::image type="content" source="images/kusto-explorer-using/manage-authorized-principals-window.png" alt-text="搜尋 + + Kusto Explorer":::

* 若要加入新的授權主體，請選取 [ **新增主體**]、提供主體詳細資料，然後確認動作。
    
    :::image type="content" source="images/kusto-explorer-using/add-authorized-principals-window.png" alt-text="搜尋 + + Kusto Explorer":::

    :::image type="content" source="images/kusto-explorer-using/confirm-add-authorized-principals.png" alt-text="搜尋 + + Kusto Explorer":::

* 若要卸載現有的授權主體，請選取 [ **捨棄主體** ]，然後確認動作。

    :::image type="content" source="images/kusto-explorer-using/confirm-drop-authorized-principals.png" alt-text="搜尋 + + Kusto Explorer":::


## <a name="next-steps"></a>後續步驟

* [Kusto.Explorer 鍵盤快速鍵](kusto-explorer-shortcuts.md)
* [Kusto.Explorer 選項](kusto-explorer-options.md)
* [針對 Kusto 進行疑難排解](kusto-explorer-troubleshooting.md)

深入了解 Kusto.Explorer 工具和公用程式：
* [Kusto.Explorer 程式碼分析器](kusto-explorer-code-analyzer.md)
* [Kusto.Explorer 程式碼導覽](kusto-explorer-codenav.md)
* [Kusto.Explorer 程式碼重構](kusto-explorer-refactor.md)
* [Kusto 查詢語言 (KQL)](../query/index.md)