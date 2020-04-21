---
title: 庫斯托 CLI - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Kusto CLI。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7443ad32b78a48f6b35be4f4b680ac6c728aedd2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524237"
---
# <a name="kusto-cli"></a>庫斯托 CLI

Kusto.Cli 是一個命令列實用程式,可用於向 Kusto 發送請求並顯示其結果。 Kusto.Cli 可以在以下幾種模式下執行:

* *REPL 模式*:使用者鍵入要針對 Kusto 執行的查詢和命令,該工具將顯示結果,然後等待下一個使用者查詢/命令。
  ("REPL"代表"讀取/eval/列印/迴圈"。

* *執行模式*:使用者提供一個或多個查詢和命令,以執行工具的命令列參數。 這些自動按順序運行,並且其結果輸出到主控台。 或者,在運行所有輸入查詢和命令后,該工具進入 REPL 模式。

* *文稿模式*:類似於執行模式,但通過檔(稱為"文稿")指定的查詢和命令。

Kusto.Cli 主要用於針對通常需要編寫代碼(例如 C# 程式或 PowerShell 文稿)的 Kusto 服務實現任務自動化。

## <a name="getting-the-tool"></a>取得工具

Kusto.Cli 是 NuGet`Microsoft.Azure.Kusto.Tools`套件 的一部分,可以[在此處](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)下載。
下載后,可以將包的`tools`資料夾提取到目標資料夾;無需額外安裝(即 xcopy 可安裝)。



## <a name="running-the-tool"></a>執行工具

Kusto.Cli 需要至少一個命令列參數才能運行。 通常,該參數是工具應連接到的 Kusto 服務的連接字串;請參考[Kusto 連接字串](../api/connection-strings/kusto.md)。 在運行工具時沒有命令列參數,或者使用一組未知的參數,或者使用`/help`交換機運行該工具,會導致向控制台發出説明消息。

例如,使用以下指令執行 Kusto.Cli 並`help`連接到 Kusto 服務,並將資料庫`Samples`上下文設定為資料庫:

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> 我們使用連接字串周圍的引號來防止 shell 應用程式(如 PowerShell)解釋連接字串中的`;`分號 () 和類似字元。

## <a name="command-line-arguments"></a>命令列引數

`Kusto.Cli.exe`*連接串*=*開關*|

*連接字串*
* 儲存所有[Kusto 連線資訊的 Kusto 連接字串](../api/connection-strings/kusto.md)。
  預設為 `net.tcp://localhost/NetDefaultDB`。

`-execute:`*查詢或指令*
* 如果指定,則以執行模式運行 Kusto.Cli;如果指定,則以執行模式運行 Kusto.Cli;運行指定的查詢或命令。 此開關可以重複,在這種情況下,查詢/命令按外觀順序運行。
  此開關不能與`-script``-scriptml`或一起使用。

`-keepRunning:`*開啟執行執行*
* 如果指定(為`true`或`false`),則在處理了`-script`所有值`-execute`或值後,啟用或禁用切換到 REPL 模式。

`-script:`*文稿檔案*
* 如果指定,則在腳本模式下運行 Kusto.Cli;載入指定的文稿檔,並按順序運行其中的查詢或命令。
  Newlines 用於分隔查詢/命令,除非行`&`以`&&`或 組合結尾,如下所述。
  此開關不能使用一起使用`-execute`。

`-scriptml:`*文稿檔案*
* 如果指定,則在腳本模式下運行 Kusto.Cli;載入指定的文稿檔,並按順序運行其中的查詢或命令。
  整個文本檔被視為單個查詢或命令。
  此開關不能使用一起使用`-execute`。

`-echo:`*開啟EchoMode*
* 如果指定(為`true`或`false`),請啟用或禁用回聲模式。
  啟用回聲模式後,輸出中重複每個查詢或命令。

`-transcript:`*成績單*  
* 若是指定,則將程式輸出寫入*文稿檔*。

`-logToConsole:`*開啟 LogtoConsole*
* 如果指定(為`true`或`false`),則啟用或禁用寫入控制台的程序輸出。

`-lineMode:`*開啟線模式*
* 如果指定,則在行輸入模式(預設值或設置為`true`) 和塊輸入模式(當`false`設置為時)和塊輸入模式之間切換。 有關這兩種模式的說明,請參閱下文,這些模式確定如何處理換行線。

**範例**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

請注意,冒號和參數值之間不應有空格。

## <a name="directives"></a>指示詞

Kusto.Cli 支援許多在工具中執行的指令,而不是送出到服務進行處理:

|指示詞                      |描述|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |獲取簡短的説明消息。|
|`q`<br>`#quit`<br>`#exit`      |退出工具。|
|`#a`<br>`#abort`               |失敗退出工具。|
|`#clip`                        |下一個查詢或命令的結果將複製到剪貼簿。|
|`#cls`                         |清除控制台螢幕。|
|`#connect`*【 連接字串*:|連接到其他 Kusto 服務(如果省略*連接 String,* 將顯示目前服務。|
|`#crp`【*Name*名稱`=`值 *:*   |設置用戶端請求屬性的值(或僅顯示它,或顯示所有值)。|
|`#crp` (`-list` | `-doc`) [*前置前置*】|列出用戶端請求屬性(按首碼或全部)。|
|`#dbcontext`【*資料庫名稱*】  |將查詢和命令使用的「上下文」資料庫更改為*資料庫名稱*(如果省略,將顯示當前上下文)。|
|`ke`*文字*                    |將指定的文字發送到正在運行的 Kusto.Explorer 進程。|
|`#loop`*計數**文字*         |多次執行文本。|
|`#qp`【*Name*名稱`=`值 *:*   |設置查詢參數的值(或僅顯示它,或顯示所有值)。 從開始/結束的單/雙引號將被修剪。|
|`#save`*檔案名稱*             |下一個查詢或命令的結果將儲存到指示的 CSV 檔中。|
|`#script`*檔案名稱*           |執行指示的腳本。|
|`#scriptml`*檔案名稱*         |執行指示的多行腳本。|

## <a name="line-input-mode-and-block-input-mode"></a>線路輸入模式與區塊輸入模式

默認情況下,Kusto.Cli 在**行輸入模式下**運行:每個換行符被解釋為查詢/命令之間的分隔符,並立即發送行以執行。

在此模式下,可以將長查詢或命令分成多行。 `&`字元作為行的最後一個字元的外觀(在新建行之前)會導致 Kusto.Cli 繼續讀取下一行。 `&&`字元作為行的最後一個字元的外觀(在新建行之前)會導致 Kusto.Cli 忽略新線並繼續讀取下一行。

或者,Kusto.Cli 還支援在**塊輸入模式下**運行:通過使用命令`-lineMode:false`行開關`#blockmode`或使用命令 ,可以指示 Kusto.Cli 假定每行都是上一行的延續,以便僅由空輸入行分隔查詢和命令。

## <a name="comments"></a>註解

Kusto.Cli 將`//`開始 新行的字串解釋為註釋行。 它忽略行的其餘部分並繼續讀取下一行。

## <a name="tool-only-options"></a>只限制工具的選項

命令                        | 效果                                                                            | 目前
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | 開啟/關閉選項計時:顯示要求所花的時間                    | TRUE
#tableon|#tableoff              | 開啟/關閉選項「tableView」:將結果集格式化為表                  | TRUE
#marson|#marsoff                | 開啟/關閉選項「marsView」:顯示第二到最後的結果集             | FALSE
#resultson|#resultsoff          | 開啟/關閉選項輸出結果集:顯示結果集                 | TRUE
#prettyon|#prettyoff            | 開啟/關閉選項「漂亮錯誤」:從錯誤中移除不必要的 goo          | TRUE
#markdownon|#markdownoff        | 開啟/關閉選項標記檢視:將表設定為標記                   | FALSE
#progressiveon|#progressiveoff  | 開啟/關閉選項「漸進式檢視」:詢問並顯示漸進式結果  | FALSE
#linemode|#blockmode            | 開啟/關閉選項線模式:單行輸入模式                          | TRUE

命令                              | 效果                                                                                                         | 預設
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | ( 啟用|關閉選項「crid」:在傳送要求之前顯示客戶端請求Id)                         | FALSE
#csvheaderson|#csvheadersoff          | ( 啟用|關閉選項「csvHeader」:在 CSV 輸出中包含標頭)                                            | TRUE
#focuson|#focusoff                    | ( 啟用|關閉選項「焦點」:刪除所有多餘的絨毛,並專注於正確的東西)                       | FALSE
#linemode|#blockmode                  | ( 啟用|關閉選項「線模式」:單行輸入模式)                                                     | TRUE
#markdownon|#markdownoff              | ( 啟用|關閉選項「標記檢視」:將表設定為「標記」格式)                                              | FALSE
#marson|#marsoff                      | ( 啟用|關閉選項「marsView」:顯示第二到最後的結果集)                                        | FALSE
#prettyon|#prettyoff                  | ( 啟用|關閉選項「漂亮錯誤」:從錯誤中移除不必要的 goo)                                     | TRUE
#querystreamingon|#querystreamingoff  | ( 啟用|關閉選項「查詢串流」:使用查詢串流終結點(僅限 Kusto 團隊))                    | FALSE
#resultson|#resultsoff                | ( 啟用|關閉選項輸出結果集:顯示結果集)                                            | TRUE
#tableon|#tableoff                    | ( 啟用|關閉選項「tableView」:將結果集設定為表的格式)                                             | TRUE
#timeon|#timeoff                      | ( 啟用|關閉選項「計時」:顯示請求所花的時間)                                               | TRUE
#typeon|#typeoff                      | ( 啟用|關閉選項「typeView」:在表檢視中顯示每欄的類型(將強制流式處理_true))  | TRUE
#v2protocolon|#v2protocoloff          | ( 啟用|關閉選項「v2 協定」:使用 v2 查詢協定,而不是 v1)                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>使用 Kusto.Cli 將結果匯出為 CSV

Kusto.Cli 支援一個特殊的用戶端指令`#save`, 以 CSV 格式將**下一個**查詢結果匯出到本地檔案。 例如,以下調用 Kusto.Cli`StormEvents``help.kusto.windows.net`將從`Samples`群集( 資料庫)中的表匯出 10 條記錄:

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>使用 Kusto.Cli 控制庫斯托.資源管理員的執行實體

可以指示 Kusto.Cli 與在電腦上運行的 Kusto.Explorer 的「主」實例進行通信,並發送要執行的查詢。 這對於想要運行許多 Kusto 查詢但不想一次又一次地啟動 Kusto.Explorer 進程的程式非常有用。 在下面的範例中,Kusto.Cli 用於重新執行查詢的協助叢集:

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

語法非常簡單: `#ke`, 後跟空格和要運行的查詢。
然後,查詢將發送到 Kusto.Explorer 的主實例(如果存在),當前群集/資料庫集位於 Kusto.Cli 中。