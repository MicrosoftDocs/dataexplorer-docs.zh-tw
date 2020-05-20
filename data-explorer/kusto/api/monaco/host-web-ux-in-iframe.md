---
title: 在**iframe**中內嵌 Web UI-Azure 資料總管
description: 本文說明如何在 Azure 資料總管的**iframe**中內嵌 Web UI。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 348a614c8b3336085a59a113f18f6858f024e8c7
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630226"
---
# <a name="embed-web-ui-in-an-iframe"></a>在 iframe 中內嵌 Web UI

Azure 資料總管 Web UI 可以內嵌在 iframe 中，並裝載于協力廠商網站。
 
:::image type="content" source="../images/host-web-ux-in-iframe/web-ux.png" alt-text="Azure 資料總管 Web UI":::

在您的網站中內嵌 Azure 資料總管 Web UX，讓您的使用者能夠：

- 編輯查詢（包括顏色標示和 intellisense 等所有語言功能）
- 以視覺化方式探索資料表架構
- 驗證 Azure AD
- 執行查詢
- 顯示查詢執行結果
- 建立多個索引標籤
- 在本機儲存查詢
- 透過電子郵件共用查詢

所有功能都已針對協助工具進行測試，並支援深色和淺色螢幕主題。

## <a name="use-monaco-kusto-or-embed-the-web-ui"></a>使用摩納哥-Kusto 或內嵌 Web UI？

摩納哥-Kusto 提供您編輯的經驗，例如完成、顏色標示、重構、重新命名和移至定義。 它會要求您建立驗證、查詢執行、結果顯示和架構探索的解決方案，但可讓您以符合需求的方式提供完整的彈性來進行使用者體驗。

內嵌 Azure 資料總管 Web UI 可讓您輕鬆地為您提供豐富的功能，但對於使用者體驗的彈性有限。 有一組固定的查詢參數，可讓您對系統的外觀和行為進行有限的控制。

## <a name="how-to-embed-the-web-ui-in-an-iframe"></a>如何在 iframe 中內嵌 Web UI

### <a name="host-the-website-in-an-iframe"></a>在 iframe 中裝載網站

將下列程式碼新增至您的網站：

```html
<iframe
  src="https://dataexplorer.azure.com/clusters/<cluster>?ibizaPortal=true"
></iframe>
```

`ibizaPortal`查詢參數會告訴 Azure 資料總管 WEB UI*不會*重新導向以取得驗證權杖。 這是必要動作，因為主控網站負責提供驗證權杖給內嵌的 iframe。

將取代 `<cluster>` 為您想要載入至 [連線] 窗格的叢集主機名稱，例如 `help.kusto.windows.net` 。 根據預設，iframe 內嵌模式不會提供從 UI 新增叢集的方式，因為假設主控網站知道所需的叢集。

### <a name="handle-authentication"></a>處理驗證

1. 當設定為「iframe 模式」（ `ibizaPortal=true` ）時，Azure 資料總管 WEB UI 不會嘗試重新導向以進行驗證。 Web UI 會使用瀏覽器所使用的訊息張貼機制來要求和接收權杖。 在頁面載入期間，下列訊息將會張貼到父視窗：

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

1. 然後，它會接聽具有下列結構的訊息：

   ```json
   {
     "type": "postToken",
     "message": "${the actual authentication token}"
   }
   ```

1. 提供的權杖應該是從[[AAD 驗證端點]](../../management/access-control/how-to-authenticate-with-aad.md#web-client-javascript-authentication-and-authorization)取得的[JWT 權杖](https://tools.ietf.org/html/rfc7519)。

> [!IMPORTANT]
> 裝載視窗必須在到期前重新整理權杖，並使用相同的機制來提供更新的權杖給應用程式。 否則，一旦權杖過期，服務呼叫將會失敗。

### <a name="feature-flags"></a>功能旗標

裝載應用程式可能會想要控制使用者體驗的某些層面。 例如，隱藏 [連線] 窗格，或停用 [連接到其他叢集]。
在此案例中，web explorer 支援功能旗標。

功能旗標可在 url 中當做查詢參數使用。 如果裝載應用程式想要停用新增其他叢集，它應該使用https://dataexplorer.azure.com/?ShowConnectionButtons=false

| 設定                 | 描述                                                                                                        | 預設值 |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------ | ------------- |
| ShowShareMenu           | 顯示 [共用] 功能表項目                                                                                           | true          |
| ShowConnectionButtons   | 顯示 [**新增連接**] 按鈕以新增新的叢集                                                            | true          |
| ShowOpenNewWindowButton | 顯示 [**在 web** UI 中開啟] 按鈕，這會開啟新的瀏覽器視窗，並指向 https://dataexplorer.azure.com [範圍] 中正確的叢集和資料庫           | false         |
| ShowFileMenu            | 顯示 [檔案] 功能表（[**下載**]、[索引標籤]、[**內容** **]** 等等）                                                 | true          |
| ShowToS                 | 從 [設定] 對話方塊顯示**Azure 資料總管的服務條款連結**                             | true          |
| ShowPersona             | 從右上角的 [設定] 功能表顯示使用者名稱                                                 | true          |
| IFrameAuth              | 若為 true，web explorer 會預期 iframe 處理驗證，並透過訊息提供權杖。 針對 iframe 案例，此程式一律為 true                                                                                                                                      | false         |
| PersistAfterEachRun     | Web explorer 通常會保存在 unload 事件中。 在 iframe 中裝載時，不一定會引發。 此旗標接著會在每次執行查詢後觸發**保存的本機狀態**。 因此，任何發生的資料遺失，只會影響從未執行過的文字，因而限制其影響          | false         |
| ShowSmoothIngestion     | 若為 true，則在資料庫上按一下滑鼠右鍵時，顯示1按下的內嵌體驗                                   | true          |
| RefreshConnection       | 若為 true，則一律會在載入頁面時重新整理架構，且永遠不會相依于本機儲存體                      | false         |
| ShowPageHeader          | 若為 true，則顯示包含 Azure 資料總管標題和設定的頁面標頭                            | true          |
| HideConnectionPane      | 若為 true，則不會顯示左側連接窗格                                                                  | false         |
| SkipMonacoFocusOnInit   | 修正在 iframe 上裝載時的焦點問題                                                                       | false         |

### <a name="feature-flag-presets"></a>功能旗標預設值

預設會一次設定一堆功能旗標。
目前只有一個預設值。

`IbizaPortal`-從預設值變更下列旗標。

```json
ShowOpenNewWindowButton: true,
PersistAfterEachRun: true,
IFrameAuth: true,
ShowPageHeader: false,                                 |
```

> [!WARNING]
> 如果您使用預設值，就無法在其頂端新增其他功能旗標。 如果您需要這樣的彈性，您應該使用個別的功能旗標。
