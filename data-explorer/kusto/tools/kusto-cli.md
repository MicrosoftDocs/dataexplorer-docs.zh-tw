---
title: Kusto CLI-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Kusto CLI。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ab82698054e4bcb851f9f05acdd62af569e0704b
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258108"
---
# <a name="kusto-cli"></a>Kusto CLI

Kusto 是命令列公用程式，可以用來將要求傳送至 Kusto，並顯示其結果。 Kusto 可在下列幾種模式中執行：

* 複寫*模式*：使用者輸入查詢和命令以對 Kusto 執行，此工具會顯示結果，然後等候下一個使用者查詢/命令。
  （「複寫」代表「讀取/評估/列印/迴圈」）。

* *執行模式*：使用者提供一或多個查詢和命令，以命令列引數的形式執行至工具。 這些會自動以循序執行，並將其結果輸出至主控台。 （選擇性）若要執行所有輸入查詢和命令，此工具會進入複寫模式。

* *腳本模式*：類似于執行模式，但使用透過檔案指定的查詢和命令（稱為「腳本」）。

Kusto 主要是提供來針對 Kusto 服務自動化工作，通常需要撰寫程式碼（例如 c # 程式或 PowerShell 腳本）。

## <a name="getting-the-tool"></a>取得工具

Kusto 是 NuGet 套件的一部分 `Microsoft.Azure.Kusto.Tools` ，可以在[這裡](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)下載。
一旦下載之後，封裝的 `tools` 資料夾就可以解壓縮到目的檔案夾; 不需要進行其他安裝（也就是可以安裝 xcopy）。



## <a name="running-the-tool"></a>執行工具

Kusto 需要至少一個命令列引數才能執行。 通常，該引數是工具應連接之 Kusto 服務的連接字串;請參閱[Kusto 連接字串](../api/connection-strings/kusto.md)。 不使用命令列引數或使用一組未知的引數來執行工具，或使用參數 `/help` 來執行，會導致將說明訊息發出至主控台。

例如，使用下列命令來執行 Kusto，並連接到 `help` Kusto 服務，並將資料庫內容設定為 `Samples` 資料庫：

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> 我們已使用引號括住連接字串，以防止 shell 應用程式（例如 PowerShell `;` ）解讀連接字串中的分號（）和類似的字元。

## <a name="command-line-arguments"></a>命令列引數

`Kusto.Cli.exe`*ConnectionString* [*切換*]

*ConnectionString*
* 保存所有 Kusto 連接資訊的[Kusto 連接字串](../api/connection-strings/kusto.md)。
  預設為 `net.tcp://localhost/NetDefaultDB`。

`-execute:`*QueryOrCommand*
* 若已指定，會在執行模式中執行 Kusto;會執行指定的查詢或命令。 這個參數可以重複，在此情況下，會依外觀循序執行查詢/命令。
  這個參數不能與或一起 `-script` 使用 `-scriptml` 。

`-keepRunning:`*EnableKeepRunning*
* 如果指定（做為 `true` 或 `false` ），則會在處理所有或值之後，啟用或停用切換至複寫模式 `-script` `-execute` 。

`-script:`*ScriptFile*
* 若已指定，會在腳本模式中執行 Kusto;系統會載入指定的指令檔，並依序執行其中的查詢或命令。
  分行符號是用來分隔查詢/命令，但以或組合結尾的行除外 `&` `&&` ，如下所述。
  這個參數不能搭配一起使用 `-execute` 。

`-scriptml:`*ScriptFile*
* 若已指定，會在腳本模式中執行 Kusto;系統會載入指定的指令檔，並依序執行其中的查詢或命令。
  整個腳本檔案會被視為單一查詢或命令。
  這個參數不能搭配一起使用 `-execute` 。

`-echo:`*EnableEchoMode*
* 如果指定（做為 `true` 或 `false` ），會啟用或停用 echo 模式。
  啟用 echo 模式時，會在輸出中重複每個查詢或命令。

`-transcript:`*TranscriptFile*  
* 若已指定，會將程式輸出寫入至*TranscriptFile*。

`-logToConsole:`*EnableLogToConsole*
* 如果指定（做為 `true` 或 `false` ），會啟用或停用將程式輸出寫入主控台。

`-lineMode:`*EnableLineMode*
* 如果指定，則會在行輸入模式（預設值或設定為時 `true` ）與封鎖輸入模式（設定為時 `false` ）之間切換。 請參閱下文以瞭解這兩種模式的說明，以決定如何處理分行符號。

**範例**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

請注意，冒號和引數值之間不應該有空格。

## <a name="directives"></a>指示詞

Kusto 支援多個在工具中執行的指示詞，而不是傳送至服務進行處理：

|指示詞                      |描述|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |取得簡短的說明訊息。|
|`q`<br>`#quit`<br>`#exit`      |結束工具。|
|`#a`<br>`#abort`               |結束工具 abortively。|
|`#clip`                        |下一個查詢或命令的結果將會複製到剪貼簿。|
|`#cls`                         |清除主控台畫面。|
|`#connect`*[ConnectionString*]|連接至不同的 Kusto 服務（如果已省略*ConnectionString* ，則會顯示目前的）。|
|`#crp`[*名稱*[ `=` *值*]]   |設定用戶端要求屬性的值（或只顯示或顯示所有值）。|
|`#crp`（ `-list` \| `-doc` ） [*前置*詞]|列出用戶端要求屬性（依前置詞或全部）。|
|`#dbcontext`[*DatabaseName*]  |將查詢和命令所使用的「內容」資料庫變更為*DatabaseName* （如果省略，則會顯示目前的內容）。|
|`ke`*文字*                    |將指定的文字傳送至執行中的 Kusto 處理常式。|
|`#loop`*計數**文字*         |執行文字數次。|
|`#qp`[*名稱*[ `=` *值*]]   |設定查詢參數的值（或只是顯示它，或顯示所有值）。 將會修剪開頭/結尾的單引號/雙引號。|
|`#save`*檔案名*             |下一個查詢或命令的結果將會儲存至指定的 CSV 檔案。|
|`#script`*檔案名*           |執行指定的腳本。|
|`#scriptml`*檔案名*         |執行指定的多行腳本。|

## <a name="line-input-mode-and-block-input-mode"></a>線路輸入模式和封鎖輸入模式

根據預設，Kusto 會在「**行輸入」模式下**執行：每個分行符號會轉譯為查詢/命令之間的分隔符號，而這一行會立即傳送以供執行。

在此模式中，可以將長的查詢或命令分成多行。 字元的外觀 `&` 做為行的最後一個字元（在分行符號之前）會導致 Kusto 繼續讀取下一行。 字元的外觀 `&&` 做為行的最後一個字元（在分行符號之前）會導致 Kusto 忽略分行符號，並繼續讀取下一行。

此外，Kusto 也支援以**區塊輸入模式**執行：使用命令列參數 `-lineMode:false` 或使用命令 `#blockmode` ，可以指示 Kusto，假設每一行都是前一行的接續，因此查詢和命令只會以空白的輸入行分隔。

## <a name="comments"></a>註解

Kusto 會解讀以 `//` 批註行開頭新行的字串。 它會忽略行的其餘部分，並繼續讀取下一行。

## <a name="tool-only-options"></a>僅限工具選項

命令                        | 效果                                                                            | 已經
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | 啟用/停用選項 [計時]：顯示要求的時間                    | TRUE
#tableon|#tableoff              | 啟用/停用選項 ' tableView '：將結果集格式化為資料表                  | TRUE
#marson|#marsoff                | 啟用/停用選項 ' marsView '：顯示第二個到最後一個結果集             | FALSE
#resultson|#resultsoff          | 啟用/停用選項 ' outputResultsSet '：顯示結果集                 | TRUE
#prettyon|#prettyoff            | 啟用/停用選項 ' prettyErrors '：從錯誤移除不必要的 goo          | TRUE
#markdownon|#markdownoff        | 啟用/停用選項 ' markdownView '：將資料表格式化為 MarkDown                   | FALSE
#progressiveon|#progressiveoff  | 啟用/停用選項 ' progressiveView '：要求並顯示漸進式結果  | FALSE
#linemode|#blockmode            | 啟用/停用選項 ' lineMode '：單行輸入模式                          | TRUE

命令                              | 效果                                                                                                         | 預設
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | （啟用|停用選項 ' crid '：在傳送要求之前顯示 ClientRequestId）                         | FALSE
#csvheaderson|#csvheadersoff          | （啟用|停用選項 ' csvHeaders '：在 CSV 輸出中包含標頭）                                            | TRUE
#focuson|#focusoff                    | （啟用|停用選項 [焦點]：移除所有額外的寫錯字，並將焦點放在正確的內容上）                       | FALSE
#linemode|#blockmode                  | （啟用|停用選項 ' lineMode '：單一行輸入模式）                                                     | TRUE
#markdownon|#markdownoff              | （啟用|停用選項 ' markdownView '：將資料表格式化為 MarkDown）                                              | FALSE
#marson|#marsoff                      | （啟用|停用選項 ' marsView '：顯示第二個到最後一個結果集                                        | FALSE
#prettyon|#prettyoff                  | （啟用|停用選項 ' prettyErrors '：從錯誤移除不必要的 goo）                                     | TRUE
#querystreamingon|#querystreamingoff  | （啟用|停用選項 ' queryStreaming '：使用 queryStreaming 端點（僅限 Kusto team））                    | FALSE
#resultson|#resultsoff                | （啟用|停用選項 ' outputResultsSet '：顯示結果集                                            | TRUE
#tableon|#tableoff                    | （啟用|停用選項 ' tableView '：將結果集格式化為資料表）                                             | TRUE
#timeon|#timeoff                      | （啟用|停用選項 [計時]：顯示要求的時間）                                               | TRUE
#typeon|#typeoff                      | （啟用|停用選項 ' typeView '：在資料表視圖中顯示每個資料行的類型（將強制串流 = true）  | TRUE
#v2protocolon|#v2protocoloff          | （啟用|停用選項 ' v2protocol '：使用 v2 查詢通訊協定，而不是 v1）                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>使用 Kusto 將結果匯出為 CSV

Kusto 支援特殊的用戶端命令， `#save` 用來將**下一個**查詢結果匯出至 CSV 格式的本機檔案。 例如，下列 Kusto 的調用將會從叢集 `StormEvents` `help.kusto.windows.net` （資料庫）中的資料表匯出10筆記錄 `Samples` ：

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>使用 Kusto 來控制執行中的 Kusto 實例

您可以指示 Kusto 與在機器上執行之 Kusto 的「主要」實例進行通訊，並將它傳送給它查詢以執行。 這對於想要執行多個 Kusto 查詢的程式而言非常有用，但不想再重新開機 Kusto 進程。 在下列範例中，Kusto 是用來執行抵說明叢集的查詢：

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

語法非常簡單： `#ke` ，後面接著空白字元和要執行的查詢。
然後，此查詢會傳送至 Kusto 的主要實例（如果有的話），並將目前的叢集/資料庫設定在 Kusto 中。
