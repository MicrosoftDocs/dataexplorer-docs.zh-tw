---
title: Kusto.Explorer 鍵盤快速鍵(熱鍵) - Azure 數據資源管理器 |微軟文件
description: 本文介紹了 Azure 資料資源管理器中的 Kusto.Explorer 鍵盤快捷鍵(熱鍵)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: 81d99d14831905681c2dbbc45aea4b63134a06a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523914"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Kusto.Explorer 鍵盤捷徑(熱鍵)

## <a name="application-level"></a>應用程式級

以下鍵盤快捷鍵可用於任何上下文:

|熱鍵|描述|
|-------|-----------|
|`F1`     | 開啟說明|
|`F11`    | 切換全檢視模式|
|`Ctrl`+`F1`| 切換功能區外觀 |
|`Ctrl`+`+`| 增加查詢與資料結果字型|
|`Ctrl`+`-`| 減少查詢與資料結果字型|
|`Ctrl`+`0`| 重置查詢與資料結果字型|
|`Ctrl`+`1` .. `7`| 切換到具有相應編號的查詢文件面板 (1..7)|
|`Ctrl`+`F2`|重新命名查詢編輯器面板標頭|
|`Ctrl`+`N` |開啟新的查詢編輯器|
|`Ctrl`+`O` |在新查詢編輯器中開啟查詢編輯器文稿|
|`Ctrl`+`W` |關閉活動查詢編輯器|
|`Ctrl`+`S` |儲存查詢到檔案中|
|`Shift`+`F3` | 開啟分析報告庫|
|`Shift`+`F12`| 切換應用程式的光/暗佈景主題|
|`Ctrl`+`Shift`+`O`|開啟 Kusto.資源管理員選項與設定對話框|
|`Esc`|取消執行的查詢|
|`Shift`+`F5`|取消執行的查詢|

## <a name="query-and-results-view"></a>查詢與結果檢視

編輯查詢或上下文位於結果檢視中時,可以使用以下鍵盤快捷鍵:

|熱鍵|描述|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|將查詢、深度連結和資料複製到剪貼簿|
|`Alt`+`Shift`+`C` |以 HTML 格式複製查詢與深度連結剪貼簿|
|`Alt`+`Shift`+`R` |以標記格式複製剪貼簿的查詢、深度連結和資料|
|`Alt`+`Shift`+`M` |以標記格式複製查詢和深度連結剪貼簿|
|`Ctrl`+`~` |以標記格式將查詢與資料複製到剪貼簿 |
|`Ctrl`+`Shift`+`D`|切換在資料檢視中隱藏重複行的模式|
|`Ctrl`+`Shift`+`H`|切換在資料檢視中隱藏空列的模式|
|`Ctrl`+`Shift`+`J`|切換在資料檢視中具有單個值的折疊列模式|
|`Ctrl`+`Shift`+`A`|在新查詢面板中開啟查詢分析器工具|
|`Alt`+`C`  |成像現有資料的柱形圖|
|`Alt`+`T`  |成像現有資料的時間線圖|
|`Alt`+`A`  |成像現有資料的例外時間線圖|
|`Alt`+`P`  |成像現有資料的圖形|
|`Alt`+`L`  |成像現有資料的梯層時間線圖|
|`Alt`+`V`  |成像現有資料的透檢視|
|`Ctrl`+`Shift`+`V`|顯示時間線對現有資料的透視|
|`Ctrl`+`F9`  | `show only matching rows``Ctrl`+`F`切換資料格線中客戶端文字搜尋 ( ) 行為的模式。 / `highlight matching rows` |
|`Ctrl`+`F10` |顯示詳細資訊面板 ─ 選擇為屬性格格顯示的位置|
|`Ctrl`+`F`  | 顯示當前焦點面板的搜索框。 支援`Connetions``Data Results``Query Editor`,與面板|
|`Ctrl`+`Tab`| 顯示查詢編輯器文件選擇器對話方塊。 您可以按住`Ctrl`文件,並在文件之間使用`Tab` |
|`Ctrl`+`J`|切換結果面板的外觀|
|`Ctrl`+`E`|在以下循環中切換查詢編輯器和結果面板的外觀:`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|在以下循環中切換查詢編輯器和結果面板的外觀:`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | 關注結果面板 |
|`Ctrl`+`Shift`+`T` | 焦點連接面板 |
|`Ctrl`+`Shift`+`Y` | 重點介紹查詢編輯器 |
|`Ctrl`+`Shift`+`P` | 焦點圖表面板 |
|`Ctrl`+`Shift`+`I` | 關注查詢資訊面板 |
|`Ctrl`+`Shift`+`S` | 重點關注查詢統計資訊面板 |
|`Ctrl`+`Shift`+`K` | 焦點錯誤面板 |
|`Alt`+`Ctrl`+`L`|將當前連接上下文鎖定到查詢編輯器,因此更改"Connetion"面板中的選定行對查詢編輯器上下文沒有影響。 |

## <a name="results-table-viewer"></a>結果表檢視器

當結果檢視(表)處於活動狀態的鍵盤焦點時,可以使用以下鍵盤快捷鍵:

|熱鍵|描述|
|-----------|-----------|
|`Ctrl`+`Q` |顯示目前欄位選單|
|`Ctrl`+`S` |切換目前欄位|
|`Ctrl`+`U` |使用客戶端篩選開啟顯示目前欄值的面板|
|`Ctrl`+`F` | 顯示結果的搜尋框|
|`Ctrl`+`F3`| `show only matching rows``Ctrl`+`F`切換資料格線中客戶端文字搜尋 ( ) 行為的模式。 / `highlight matching rows` |

## <a name="query-editor"></a>查詢編輯器

在查詢編輯器中編輯查詢時, 可以使用以下鍵盤快速鍵:

|熱鍵|描述|
|-------|-----------|
|`F1`|當游標指向運算子或函數時 - 打開一個幫助視窗,其中包含有關運算符或函數的資訊。 如果說明主題不存在 - 開啟說明網址|
|`F5`|執行目前選取的查詢|
|`Shift`+`Enter`|執行目前選取的查詢|
|`F8`|從本地緩存獲取查詢結果。 如果結果不存在 - 執行目前選取的查詢|
|`F6`|在`Progressive Results`模式執行目前選取的查詢|
|`Ctrl`+`F5` | 預覽選取查詢的結果(顯示很少的結果和總計數)|
|`Ctrl`+`Shift`+`Space`| 將資料儲存格選擇作為篩選器插入到查詢中|
|`Ctrl`+`Space`| 強制感知規則檢查。 任何符合的規則中都顯示可能的選項 |
|`Ctrl`+`Enter`| 新增`pipe`符號並移至新行|
|`Ctrl`+`Z`| 復原 |
|`Ctrl`+`Y`| 重做 |
|`Ctrl`+`L`| 刪除目前的列|
|`Ctrl`+`D`| 刪除目前的列| 
|`Ctrl`+`F`| 開啟`Find and Replace`對話框 |
|`Ctrl`+`G`| 開啟`Go-to line`對話框 |
|`Ctrl`+`F8` | 顯示過去 3 天查詢 |
|`Ctrl`• 支架 | 當游標位於`(`括弧符號時`)``[``]``{``}`:、、、、、、、 移至匹配的開盤或右括弧 |
|`Ctrl`+`Shift`+`Q` | 增強目前查詢 |
|`Ctrl`+`Shift`+`L` | 將目前查詢或選擇小寫 |
|`Ctrl`+`Shift`+`U` |  將目前查詢或選擇大寫 |
|`Ctrl`+`Mouse wheel up`| 放大縮小字型功能 放大縮小字型功能| 
|`Ctrl`+`Mouse wheel down`| 減少查詢編輯器的字型|
|`Alt`+`P` | 開啟查詢參數對話框 |
|`F2`| 在編輯器對話框開啟目前的行/選擇文字 |
|`Ctrl`+`F6`| 執行 KQL 靜態查詢分析以偵測常見問題 |
|`F12`| 導覽到符號定義 |
|`Ctrl`+`F12`| 尋找目前的符號的所有參照 |
|`Alt`+`Home`| 導覽到符號定義 |
|`Alt`+`Ctrl`+`M`| 擷取目前選取的文字或表格表示式為 let 語句 |
|`Ctrl`+`.`| 擷取目前選取的文字或表格表示式為 let 語句 |
|`Ctrl`+`R`, `Ctrl`+`R` | 重新命名目前的符號 |
|`Ctrl`+`K`, `Ctrl`+`D` | 將目前時間戳插入為「不期」文字 |
|`Ctrl`+`K`, `Ctrl`+`R` | 插入`range x from 1 to 1 step 1`程式碼段 |
|`Ctrl`+`K`, `Ctrl`+`C` | 註解目前行或選取的列 |
|`Ctrl`+`K`, `Ctrl`+`F` | 增強目前查詢 |
|`Ctrl`+`K`, `Ctrl`+`V` | 複製目前查詢(將新增到目前查詢文件的末尾 ) |
|`Ctrl`+`K`, `Ctrl`+`U` | 取消註解目前的行或選取的列 |
|`Ctrl`+`K`, `Ctrl`+`S` | 將目前的行或選取的行轉換為多行字串文字 |
|`Ctrl`+`K`, `Ctrl`+`M` | 刪除多行攪拌字面標記 (反`Ctrl`+`K``Ctrl`+`S`轉 ) |
|`Ctrl`+`M`, `Ctrl`+`M` | 切換概述目前查詢的延伸 |
|`Ctrl`+`M`, `Ctrl`+`L` | 切換概述文件中所有查詢的擴充 |

## <a name="json-viewer"></a>JSON 檢視器

以下鍵盤支撐可以從結果 JSON 檢視器中使用(在結果檢視單元格中按兩下類似 JSON 的值時顯示):

|熱鍵|描述|
|-------|-----------|
|`Ctrl`+`Up Arrow`|導覽到父級|
|`Ctrl`+`Right Arrow`|展開目前的節點(一個等級)|
|`Ctrl`+`Left Arrow`|收起目前的節點(一層)|
|`Ctrl`+`.`|切換目前節點的延伸(所有子等級展開/折疊)
|`Ctrl`+`Shift`+`.`|切換目前節點父節點的延伸(所有子等級展開/折疊)|

## <a name="connection-panel"></a>連接面板

以下鍵盤支撐可以從結果 JSON 檢視器中使用(在結果檢視單元格中按兩下類似 JSON 的值時顯示):

|熱鍵|描述|
|-------|-----------|
|`Ctrl`+`Up`箭號|導覽到父級|
|`Ctrl`+`Right`箭號|展開目前的節點(一個等級)|
|`Ctrl`+`Left`箭號|收起目前的節點(一層)|
|`Ctrl`+`Shift`+`L`|收起所有等級|
|`Ctrl`+`R`|更新目前選取的連線|
|`Insert`|新增連線|
|`Del`|刪除目前連線|
|`Ctrl`+`E`|編輯目前所選取的連線|
|`Ctrl`+`T`|使用目前所選取的連線開啟新的查詢編輯器|

## <a name="diagnostics-and-monitoring"></a>診斷和監控

以下鍵盤快捷鍵可從`Monitoring`功能區獲得。

|熱鍵|描述|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|執行叢集診斷流|