---
title: 在 iframe 內嵌入 Web UI - Azure 資料資源管理員 ( Azure) 的資料資源管理員 。微軟文件
description: 本文介紹在 Azure 資料資源管理器中的 iframe 中嵌入 Web UI。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9c5469a577c3e09cd433ec250f9215e5f450d5f5
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524458"
---
# <a name="embed-web-ui-in-an-iframe"></a>將 Web UI 嵌入到 iframe 中

Azure 資料資源管理員 Web UI 可以嵌入到 iframe 中,並託管在第三方網站中。

![alt 文字](../images/web-ux.jpg "Azure 資料資源管理員 Web UI")

在網站內嵌入 Azure 資料資源管理員 Web UX 使用戶能夠執行以下操作:

- 編輯查詢(包括所有語言功能,如著色和感知)
- 直觀地瀏覽表架構
- 向 AAD 進行認證
- 執行查詢
- 顯示查詢執行結果
- 建立多個選項卡
- 在本地儲存查詢
- 透過電子郵件分享查詢

所有功能都經過無障礙測試,並支援黑暗和淺色主題。

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>使用摩納哥-庫塞托還是嵌入 Web UI?

Monaco-Kusto 為您提供了完成、著色、重構、重命名和去定義等編輯體驗,它要求您構建一個解決方案,以便圍繞該解決方案進行身份驗證、查詢執行、結果顯示和架構探索。 另一方面,您可以完全靈活地打造適合您需求的用戶體驗。

嵌入 Azure 資料資源管理器 Web UI 時,只需很少精力即可提供廣泛的功能,但在使用者體驗方面的靈活性有限。 有一組固定的查詢參數,允許對系統的外觀和行為進行有限的控制。

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>如何將 Web UI 嵌入到 iframe 中

### <a name="host-the-website-in-iframe"></a>在 iframe 中託管網站

加入您的網站加入以下代碼:

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

查詢`ibizaPortal`參數告訴 Azure 資料資源管理員 Web UI_不要_重定向以獲取身份驗證權杖。 這是必要的,因為託管網站負責向嵌入式 iframe 提供身份驗證權杖。

替換為`<cluster>`要載入到連接窗格(for`example: help.kusto.windows.net`的)的群集的主機名。 默認情況下,iframe 嵌入模式不提供從 UI 添加群集的方法,因為假設託管網站知道所需的群集。

### <a name="handle-authentication"></a>處理認證

1. 當設置為"iframe模式"`ibizaPortal=true`()時,Azure 資料資源管理器 Web UI 不會嘗試重定向以進行身份驗證。 Web UI 將使用瀏覽器用來請求和接收權杖的消息發佈機制。 在頁面載入期間,以下訊息將發佈到父視窗:

   ```javascript
   window.parent.postMessage(
     {
       signature: "queryExplorer",
       type: "getToken"
     },
     "*"
   );
   window.addEventListener(
     "message",
     event => this.handleIncomingMessage(event),
     false
   );
   ```

1. 然後,它將偵聽具有以下結構的消息:

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. 提供的權杖應是從[[AAD 認證終結點]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)取得的[JWT 權杖](https://tools.ietf.org/html/rfc7519)。

> [!IMPORTANT]
> 託管視窗負責在過期之前刷新權杖,並使用同一機制向應用程式提供更新的權杖。 否則,一旦令牌過期,服務調用將始終失敗。

### <a name="feature-flags"></a>功能旗標

託管應用程式可能希望扭曲用戶體驗的某些方面。 例如 - 隱藏連接窗格,或關閉連線到其他群集。
對於此方案,Web 資源管理員支援功能標誌。

要素標誌可以在 URL 中用作查詢參數。 例如,如果託管應用程式希望禁用添加其他群集,則應使用https://dataexplorer.azure.com/?ShowConnectionButtons=false

| 設定                 | 描述                                                                                                                                                                                                                                                                                       | 預設值 |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| 顯示共享選單           | 顯示共享選單項目                                                                                                                                                                                                                                                                          | true          |
| 顯示連線按鈕   | 顯示新增連線按鈕以新增新叢集                                                                                                                                                                                                                                               | true          |
| 顯示開啟新視窗按鈕 | 在 Web UI 按鈕中顯示" 這將開啟新的瀏覽器視窗,該視窗將指向https://dataexplorer.azure.com在作用網域中具有正確的叢集和資料庫                                                                                                                                   | false         |
| 顯示檔案選單            | 顯示檔案選單(下載選項卡內容 ETC)                                                                                                                                                                                                                                                     | true          |
| 顯示                 | 從設定對話框顯示 Azure 資料資源管理員的服務條款連結                                                                                                                                                                                                                | true          |
| 顯示個人個人             | 在右上角與設定選單中顯示使用者名稱                                                                                                                                                                                                                                        | true          |
| IFrameAuth              | 如果為 true,Web 資源管理員將希望 iframe 處理身份驗證並通過消息提供權杖。 對於 iframe 方案,這始終正確                                                                                                                                              | false         |
| 每次執行後保持     | 通常,Web 資源管理器將堅持在卸載事件中。 在 iframe 中託管時,在某些情況下不會觸發。 因此,此標誌將在每次查詢運行后觸發本地狀態。 因此,發生的任何數據丟失都只會影響從未運行過的文本,從而限制影響。 | false         |
| 顯示平滑     | 如果為 true,請顯示右鍵按這裏的一鍵式引入體驗                                                                                                                                                                                                                  | true          |
| 刷新連線       | 如果為 true,則在載入頁面時始終刷新架構,並且從不依賴於本地存儲                                                                                                                                                                                                      | false         |
| 顯示頁頭          | 如果為 true,則顯示頁面標題(包括 Azure 資料資源管理員標題、設定和其他)                                                                                                                                                                                                 | true          |
| 隱藏連接窗格      | 如果為 true,則不顯示左側連接窗格                                                                                                                                                                                                                                                | false         |
| 跳過摩納哥焦點   | 修復了在 iframe 上託管時的焦點問題                                                                                                                                                                                                                                                          | false         |

### <a name="feature-flag-presets"></a>功能旗標預先設定

預設將同時設置一組功能標誌。
今天,我們有一個預設。

`IbizaPortal`- 變更預設值中的以下旗標:

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> 如果使用預設,您將無法在上面添加其他功能標誌。 如果需要這種靈活性,則應使用單獨的要素標誌。
