---
title: Kusto. Explorer 鍵盤快速鍵（熱鍵）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Kusto 鍵盤快速鍵（熱鍵）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: a5bda2079adc06ea1bca5af65d89418ef5c293f6
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264669"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Kusto. Explorer 鍵盤快速鍵（熱鍵）

## <a name="application-level"></a>應用層級

您可以從任何內容使用下列鍵盤快速鍵：

|快速鍵|描述|
|-------|-----------|
|`F1`     | 開啟 [說明]|
|`F11`    | 切換完整視圖模式|
|`Ctrl`+`F1`| 切換功能區外觀 |
|`Ctrl`+`+`| 增加查詢和資料結果字型|
|`Ctrl`+`-`| 減少查詢和資料結果字型|
|`Ctrl`+`0`| 重設查詢和資料結果字型|
|`Ctrl`+`1` .. `7`| 切換至具有各自數位的查詢檔面板（1. 7）|
|`Ctrl`+`F2`|[查詢編輯器] 面板的重新命名標頭|
|`Ctrl`+`N` |開啟新的查詢編輯器|
|`Ctrl`+`O` |在新的查詢編輯器中開啟查詢編輯器腳本|
|`Ctrl`+`W` |關閉使用中的查詢編輯器|
|`Ctrl`+`S` |將查詢儲存到檔案中|
|`Shift`+`F3` | 開啟分析報表圖庫|
|`Shift`+`F12`| 切換應用程式的淺色/深色主題|
|`Ctrl`+`Shift`+`O`|開啟 [Kusto] [選項和設定] 對話方塊|
|`Esc`|取消正在執行的查詢|
|`Shift`+`F5`|取消正在執行的查詢|

## <a name="query-and-results-view"></a>查詢和結果檢視

您可以使用下列鍵盤快速鍵搭配 [查詢編輯器]，或在 [結果] 視圖中顯示內容：

|快速鍵|描述|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|將查詢、深層連結和資料複製到剪貼簿|
|`Alt`+`Shift`+`C` |以 HTML 格式複製查詢並深入連結剪貼簿|
|`Alt`+`Shift`+`R` |以 Markdown 格式複製查詢、深層連結並將資料放在剪貼簿中|
|`Alt`+`Shift`+`M` |以 Markdown 格式複製查詢並深入連結剪貼簿|
|`Ctrl`+`~` |以 markdown 格式將查詢和資料複製到剪貼簿 |
|`Ctrl`+`Shift`+`D`|在資料檢視中隱藏重復資料列的切換模式|
|`Ctrl`+`Shift`+`H`|切換資料檢視中隱藏空白資料行的模式|
|`Ctrl`+`Shift`+`J`|在資料檢視中使用單一值切換折迭資料行的模式|
|`Ctrl`+`Shift`+`A`|在新的查詢面板中開啟 Query Analyzer 工具|
|`Alt`+`C`  |呈現現有資料的直條圖|
|`Alt`+`T`  |呈現現有資料的時間軸圖表|
|`Alt`+`A`  |呈現現有資料的異常時間軸圖表|
|`Alt`+`P`  |呈現現有資料的圓形圖|
|`Alt`+`L`  |呈現現有資料的階梯時程表圖表|
|`Alt`+`V`  |呈現現有資料的樞紐分析表|
|`Ctrl`+`Shift`+`V`|顯示現有資料的時間軸資料|
|`Ctrl`+`F9`  | 切換 `show only matching rows` / `highlight matching rows` 資料格內用戶端文字搜尋（ `Ctrl` + `F` ）行為的模式。 |
|`Ctrl`+`F10` |顯示詳細資料面板-選取的資料列會顯示為屬性方格|
|`Ctrl`+`F`  | 顯示目前為焦點之面板的 [搜尋] 方塊。 支援 `Connetions` 、 `Data Results` 和 `Query Editor` 面板|
|`Ctrl`+`Tab`| 顯示 [查詢編輯器檔選取器] 對話方塊。 您可以使用來保存 `Ctrl` 和切換檔`Tab` |
|`Ctrl`+`J`|切換結果面板的外觀|
|`Ctrl`+`E`|以迴圈方式切換 [查詢編輯器] 和 [結果] 面板的外觀：`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|以迴圈方式切換 [查詢編輯器] 和 [結果] 面板的外觀：`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | 聚焦于 [結果] 面板 |
|`Ctrl`+`Shift`+`T` | 專注于連接面板 |
|`Ctrl`+`Shift`+`Y` | 著重于查詢編輯器 |
|`Ctrl`+`Shift`+`P` | 聚焦于圖表面板 |
|`Ctrl`+`Shift`+`I` | 著重于查詢資訊面板 |
|`Ctrl`+`Shift`+`S` | 著重于查詢統計資料面板 |
|`Ctrl`+`Shift`+`K` | 著重于錯誤面板 |
|`Alt`+`Ctrl`+`L`|鎖定查詢編輯器的目前連接內容，因此變更 [連接] 面板中選取的資料列不會影響查詢編輯器內容。 |

## <a name="results-table-viewer"></a>結果資料表檢視器

當結果檢視（資料表）處於作用中的鍵盤焦點時，可以使用下列鍵盤快速鍵：

|快速鍵|描述|
|-----------|-----------|
|`Ctrl`+`Q` |顯示目前的資料行內容功能表|
|`Ctrl`+`S` |切換目前的資料行排序|
|`Ctrl`+`U` |開啟面板，顯示目前的資料行值與用戶端篩選|
|`Ctrl`+`F` | 顯示結果的搜尋方塊|
|`Ctrl`+`F3`| 切換 `show only matching rows` / `highlight matching rows` 資料格內用戶端文字搜尋（ `Ctrl` + `F` ）行為的模式。 |

## <a name="query-editor"></a>查詢編輯器

在 [查詢編輯器] 中編輯查詢時，可以使用下列鍵盤快速鍵：

|快速鍵|描述|
|-------|-----------|
|`F1`|當游標指向運算子或函式時，會開啟 [說明] 視窗，其中包含運算子或函數的相關資訊。 如果 [說明] 主題不存在，則會開啟 [說明 URL]|
|`F5`|執行目前選取的查詢|
|`Shift`+`Enter`|執行目前選取的查詢|
|`F8`|從本機快取提取查詢結果。 如果結果不存在-執行目前選取的查詢|
|`F6`|在模式中執行目前選取的查詢 `Progressive Results`|
|`Ctrl`+`F5` | 預覽所選查詢的結果（顯示較少的結果和總計數）|
|`Ctrl`+`Shift`+`Space`| 將資料格選取範圍做為篩選準則插入查詢|
|`Ctrl`+`Space`| 強制執行 IntelliSense 規則檢查。 可能的選項會顯示在任何符合的規則中 |
|`Ctrl`+`Enter`| 加入 `pipe` 符號並移至新行|
|`Ctrl`+`Z`| 復原 |
|`Ctrl`+`Y`| 取消復原 |
|`Ctrl`+`L`| 刪除目前的行|
|`Ctrl`+`D`| 刪除目前的行| 
|`Ctrl`+`F`| 開啟 `Find and Replace` 對話方塊 |
|`Ctrl`+`G`| 開啟 `Go-to line` 對話方塊 |
|`Ctrl`+`F8` | 過去3天內顯示我的查詢 |
|`Ctrl`+ 括弧 | 當游標位於括弧符號時： `(` 、 `)` 、 `[` 、 `]` 、 `{` 、 `}` -將游標移至相符的左或右括弧 |
|`Ctrl`+`Shift`+`Q` | 修飾目前的查詢 |
|`Ctrl`+`Shift`+`L` | 將目前的查詢或選取範圍設為小寫 |
|`Ctrl`+`Shift`+`U` |  將目前的查詢或選取範圍設為大寫 |
|`Ctrl`+`Mouse wheel up`| 增加查詢編輯器的字型|
|`Ctrl`+`Mouse wheel down`| 減少查詢編輯器的字型|
|`Alt`+`P` | 開啟 [查詢參數] 對話方塊 |
|`F2`| 在編輯器對話方塊中開啟目前的行/選取的文字 |
|`Ctrl`+`F6`| 執行 KQL 靜態查詢分析以偵測常見的問題 |
|`F12`| 流覽至符號的定義 |
|`Ctrl`+`F12`| 尋找目前符號的所有參考 |
|`Alt`+`Home`| 流覽至符號的定義 |
|`Alt`+`Ctrl`+`M`| 將目前選取的常值或表格式運算式解壓縮為 let 語句 |
|`Ctrl`+`.`| 將目前選取的常值或表格式運算式解壓縮為 let 語句 |
|`Ctrl`+`R`, `Ctrl`+`R` | 重新命名目前的符號 |
|`Ctrl`+`K`, `Ctrl`+`D` | 將目前的時間戳記插入為日期時間常值 |
|`Ctrl`+`K`, `Ctrl`+`R` | 插入 `range x from 1 to 1 step 1` 程式碼片段 |
|`Ctrl`+`K`, `Ctrl`+`C` | 批註目前的行或選取的行 |
|`Ctrl`+`K`, `Ctrl`+`F` | 修飾目前的查詢 |
|`Ctrl`+`K`, `Ctrl`+`V` | 複製目前的查詢（將它附加至目前的查詢檔結尾） |
|`Ctrl`+`K`, `Ctrl`+`U` | 取消批註目前的一行或選取的行 |
|`Ctrl`+`K`, `Ctrl`+`S` | 將目前的行或選取的行變成多行字串常值 |
|`Ctrl`+`K`, `Ctrl`+`M` | 移除多行字串常值標記（反轉 `Ctrl` + `K` ， `Ctrl` + `S` ） |
|`Ctrl`+`M`, `Ctrl`+`M` | 切換大綱目前查詢的展開 |
|`Ctrl`+`M`, `Ctrl`+`L` | 切換大綱檔中所有查詢的展開 |

## <a name="json-viewer"></a>JSON 檢視器

您可以從 [結果 JSON 檢視器] 中使用下列鍵盤快速鍵。
如果您在結果 view 資料格中按兩下類似 JSON 的值，就會顯示：

|快速鍵|描述|
|-------|-----------|
|`Ctrl`+`Up Arrow`|流覽至父系|
|`Ctrl`+`Right Arrow`|展開目前的節點（一層級）|
|`Ctrl`+`Left Arrow`|折迭目前節點（一層級）|
|`Ctrl`+`.`|切換目前節點的展開（展開/折迭所有子層級）
|`Ctrl`+`Shift`+`.`|切換目前節點父系的展開（所有子層級展開/折迭）|

## <a name="connection-panel"></a>連接面板

您可以從 [結果 JSON 檢視器] 中使用下列鍵盤快速鍵。
如果您在結果 view 資料格中按兩下類似 JSON 的值，就會顯示：

|快速鍵|描述|
|-------|-----------|
|`Ctrl`+`Up`箭號|流覽至父系|
|`Ctrl`+`Right`箭號|展開目前的節點（一層級）|
|`Ctrl`+`Left`箭號|折迭目前節點（一層級）|
|`Ctrl`+`Shift`+`L`|折迭所有層級|
|`Ctrl`+`R`|重新整理目前選取的連接|
|`Insert`|加入新的連接|
|`Del`|刪除目前的連接|
|`Ctrl`+`E`|編輯目前選取的連接|
|`Ctrl`+`T`|使用目前選取的連接開啟新的查詢編輯器|

## <a name="diagnostics-and-monitoring"></a>診斷和監控

您可以從功能區取得下列鍵盤快速鍵 `Monitoring` 。

|快速鍵|描述|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|執行叢集診斷流程|
