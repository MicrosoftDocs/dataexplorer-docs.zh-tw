---
title: UI 深層連結 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 UI 深層連結。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: c9535ced274ccdbe38f8d9a765e98d11e7b381e5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523676"
---
# <a name="ui-deep-links"></a>UI 深層連結

REST API 提供了一個深層連結`GET`功能, 允許 HTTP 請求將呼叫方重定向到 UI 工具。 例如,您可以製作一個 URI 來打開 Kusto.Explorer 工具,自動配置它用於特定的叢集和資料庫,運行特定查詢並將其結果顯示給使用者。

UI 深層連結 REST API:

* 群集(強制)通常隱式定義,作為實現 REST API 的服務,但可以通過`uri`指定 URI 查詢參數 來重寫。

* 資料庫(可選)被指定為URI路徑的第一個也是唯一的片段。 資料庫對於查詢是必需的,並且對於控制命令是可選的。

* 查詢或控制命令(可選)通過使用URI查詢參數`query`或URI查詢參數`querysrc`(指向保存查詢的Web資源)指定。
  `query`可用於查詢或控制項指令本身的文字(使用 HTTP 查詢參數編碼編碼)。 或者,它可用於查詢或控制項指令文本 gzip 的 base64 編碼(因此可以壓縮長查詢,以便它們適合預設的瀏覽器 URI 長度限制)。

* 叢集連接的名稱(可選)是通過使用URI查詢參數`name`指定的。

* 使用可選的`web`URI 查詢參數指定 UI 工具。
  `web=0`指示桌面應用程式 Kusto.Explorer。 `web=1`表示 Kusto.WebExplorer Web 應用程式。
  `web=2`是 Kusto.WebExplorer 的舊版本(基於應用程式見解分析)。 `web=3`是庫sto.WebExplorer,配置檔為空(以前打開的選項卡或群集將不可用)。 最後,`web`可以`saw=1`替換 查詢參數,以指示 Kusto.Explorer 的 SAW 版本。

以下是連結的幾個範例:

* `https://help.kusto.windows.net/`:當使用者代理(如瀏覽器)發出請求時`GET /`,它將重定向到配置為查詢群集`help`的 默認 UI 工具。
* `https://help.kusto.windows.net/Samples`:當使用者代理(如瀏覽器)發出請求時`GET /Samples`,它將重定向到配置為查詢群`help``Samples`集 資料庫的默認 UI 工具。
* `http://help.kusto.windows.net/Samples?query=StormEvents`:當使用者(如瀏覽器)`GET /Samples?query=StormEvents`發出請求時,它將重定向到配置為`help`查詢`Samples`群集 資料庫並`StormEvents`發出查詢的 默認 UI 工具。

> [!NOTE]
> 深層鏈接URI不需要身份驗證,因為身份驗證由用於重定向的 UI 工具執行。
> 任何`Authorization`HTTP 標頭(如果提供)都將被忽略。

> [!IMPORTANT]
> 出於安全原因,UI 工具不會自動執行控制命令,`query`即使或`querysrc`在深層連結中指定也是如此。

## <a name="deep-linking-to-kustoexplorer"></a>深度連結到庫斯托資源管理員

此 REST API 執行重定向,安裝並運行 Kusto.Explorer 桌面用戶端工具,該啟動參數是專門設計的啟動參數,可打開到特定 Kusto 引擎群集的連接,並針對該群集執行查詢。

* 路徑: `/` [*資料庫名稱*】]
* 動詞:`GET`
* 查詢字串:`web=0`

> [!NOTE]
> 有關啟動 Kusto.Explorer 的重定向 URI 語法的說明,請參閱[與 Kusto.Explorer 的深層連結](../../tools/kusto-explorer.md#deep-linking-queries)。

## <a name="deep-linking-to-kustowebexplorer"></a>深度連結到 Kusto.WebExplorer

此 REST API 執行重定向到 Kusto.WebExplorer,一個 Web 應用程式。

* 路徑: `/` [*資料庫名稱*】]
* 動詞:`GET`
* 查詢字串:`web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>在 URI 中指定查詢或控制命令

指定 URI 查詢字`query`串 參數時,必須根據編碼 HTML 規則的 URI 查詢字串對其進行編碼。 或者,查詢或控制命令的文本可以通過 gzip 進行壓縮,然後通過 base64 編碼進行編碼。 這允許您發送較長的查詢或控制命令(因為後一種編碼方法會導致較短的 URI)。

## <a name="specifying-the-query-or-control-command-by-indirection"></a>透過間接指定查詢或控制命令

如果查詢或控制命令很長,即使使用 gzip/base64 對其進行編碼也會超過使用者代理的最大 URI 長度。 或者,提供URI查詢`querysrc`字串參數,其值是指向保存查詢或控制項指令文本的 Web 資源的簡短 URI。

例如,這可以是 Azure Blob 儲存託管檔的 URI。

> [!NOTE]
> 如果深層連結指向 Web 應用程式 UI 工具,則必須將提供查詢或控制`querysrc`命令(即提供 URI 的服務)的 Web`dataexplorer.azure.com`服務配置為支援 CORS。
>
> 此外,如果該服務需要身份驗證/授權資訊,則必須作為URI本身的一部分提供該資訊。
>
> 例如,如果`querysrc`指向 Azure Blob 儲存中的 Blob,則必須將儲存帳戶配置為支援 CORS,並會使 Blob 本身公開(以便無需安全聲明即可下載),或者向 URI 添加適當的 Azure 儲存 SAS。 CORS 設定可以從 Azure[門戶](https://portal.azure.com/)或[Azure 儲存資源管理員](https://azure.microsoft.com/features/storage-explorer/)完成。
> 請參考[Azure 儲存中的 CORS 支援](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。

