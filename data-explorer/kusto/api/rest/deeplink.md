---
title: UI 深層連結-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 UI 深層連結。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 412e489365daabfdde7de8cd61e398100f4bc3ef
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337392"
---
# <a name="ui-deep-links"></a>UI 深層連結

REST API 提供深層連結功能，可讓 HTTP `GET` 要求將呼叫端重新導向至 UI 工具。 例如，您可以製作一個開啟 Kusto 工具的 URI、針對特定叢集和資料庫自動設定它、執行特定查詢，並向使用者顯示其結果。

UI 深層連結 REST API：

* 叢集 (強制性) 通常是以隱含方式定義，做為執行 REST API 的服務，但可以藉由指定 URI 查詢參數加以覆寫 `uri` 。

* 資料庫 (選擇性的) 會指定為 URI 路徑的第一個和唯一片段。 資料庫是查詢的必要項，而且是控制命令的選擇性選項。

* 您可以使用 URI 查詢參數指定查詢或控制命令 (選擇性的) `query` ，或使用 uri 查詢參數 `querysrc` (，指向保存查詢) 的 web 資源。
  `query` 可以在查詢或控制命令本身的文字中使用， (使用 HTTP 查詢參數編碼) 進行編碼。 或者，您也可以在查詢或控制命令文字的 gzip 的 base64 編碼中使用它， (讓您可以壓縮較長的查詢，使其符合) 的預設瀏覽器 URI 長度限制。

* 叢集連接的名稱 (選擇性的) 是使用 URI 查詢參數所指定 `name` 。

* UI 工具是使用 `web` 選擇性的 URI 查詢參數所指定。
  `web=0` 表示桌面應用程式 Kusto。 `web=1` 表示 Kusto WebExplorer web 應用程式。
  `web=2` 是舊版的 Kusto. WebExplorer (以 Application Insights 分析) 為基礎。 `web=3` 是具有空白設定檔的 Kusto. WebExplorer (沒有先前開啟的索引標籤或叢集將可) 。 最後， `web` 可以取代查詢參數， `saw=1` 以表示 KUSTO 的看到版本。

以下是一些連結範例：

* `https://help.kusto.windows.net/`：當使用者代理程式 (（例如瀏覽器）) 發出要求時，會 `GET /` 將要求重新導向至設定為查詢叢集的預設 UI 工具 `help` 。
* `https://help.kusto.windows.net/Samples`：當使用者代理程式 (（例如瀏覽器）) 發出要求時，會 `GET /Samples` 將要求重新導向至設定為查詢叢集資料庫的預設 UI 工具 `help` `Samples` 。
* `http://help.kusto.windows.net/Samples?query=StormEvents`：當使用者 (例如瀏覽器) 發出 `GET /Samples?query=StormEvents` 要求時，會將要求重新導向至設定為查詢叢集資料庫的預設 UI `help` 工具 `Samples` ，併發出 `StormEvents` 查詢。

> [!NOTE]
> 深層連結 Uri 不需要驗證，因為驗證是由用來重新導向的 UI 工具所執行。
> 任何 `Authorization` HTTP 標頭（如果有的話）都會被忽略。

> [!IMPORTANT]
> 基於安全性理由，UI 工具不會自動執行控制命令，即使 `query` `querysrc` 在深層連結中指定或也是一樣。

## <a name="deep-linking-to-kustoexplorer"></a>Kusto 的深層連結

此 REST API 會執行重新導向，以使用特別設計的啟動參數來安裝和執行 Kusto，以開啟特定 Kusto 引擎叢集的連線，並針對該叢集執行查詢。

* 路徑： `/` [*DatabaseName*]
* 動詞： `GET`
* 查詢字串： `web=0`

> [!NOTE]
> 如需啟動 Kusto 的重新導向 URI 語法說明，請參閱 [Kusto 的深層連結](../../tools/kusto-explorer-using.md#deep-linking-queries) 。

## <a name="deep-linking-to-kustowebexplorer"></a>深層連結至 Kusto. WebExplorer

此 REST API 會重新導向至 web 應用程式的 Kusto WebExplorer。

* 路徑： `/` [*DatabaseName*]
* 動詞： `GET`
* 查詢字串： `web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>在 URI 中指定查詢或控制命令

指定 URI 查詢字串參數時 `query` ，它必須根據 uri 查詢字串編碼 HTML 規則進行編碼。 或者，您可以使用 gzip 壓縮查詢或控制命令的文字，然後再透過 base64 編碼進行編碼。 這可讓您將較長的查詢或控制命令 (傳送，因為後者的編碼方法會產生較短的 Uri) 。

## <a name="specifying-the-query-or-control-command-by-indirection"></a>以間接取值指定查詢或控制命令

如果查詢或控制命令很長，即使使用 gzip/base64 進行編碼，也會超過使用者代理程式的 URI 長度上限。 或者，會提供 URI 查詢字串參數 `querysrc` ，且其值是指向保存查詢或控制命令文字之 web 資源的簡短 URI。

例如，這可以是 Azure Blob 儲存體所主控之檔案的 URI。

> [!NOTE]
> 如果深層連結是 web 應用程式 UI 工具，則提供查詢或控制命令的 web 服務 (也就是提供 `querysrc` URI) 的服務必須設定為支援的 CORS `dataexplorer.azure.com` 。
>
> 此外，如果該服務需要驗證/授權資訊，則必須將其提供作為 URI 本身的一部分。
>
> 例如，如果 `querysrc` 指向 Azure Blob 儲存體中 blob 的點，則必須將儲存體帳戶設定為支援 CORS，並且將 blob 本身設為公用 (，讓它可以在不使用安全性宣告的情況下下載) 或將適當的 AZURE 儲存體 SAS 新增至 URI。 CORS 設定可以從 [Azure 入口網站](https://portal.azure.com/) 或 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)進行。
> 請參閱 [Azure 儲存體中的 CORS 支援](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。