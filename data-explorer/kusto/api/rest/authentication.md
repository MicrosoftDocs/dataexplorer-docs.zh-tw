---
title: 通過 HTTPS 進行身份驗證 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中通過 HTTPS 進行身份驗證。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 4b6fbf5bb34dc3ff52938c7042778a7e49fd5faf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503038"
---
# <a name="authentication-over-https"></a>透過 HTTPS 進行認證

使用 HTTPS 時,該服務`Authorization`支援用於 執行身份驗證的標準 HTTP 標頭。

支援的 HTTP 認證方法包括:

* **Azure 活動目錄**`bearer`,通過 方法。

使用 Azure 的目錄進行認證時`Authorization`, 標頭格式為:

```txt
Authorization: bearer TOKEN
```

呼叫`TOKEN`者透過與 Azure 的目錄服務通訊取得的存取權杖在哪裡,具有以下屬性:

* 資源是服務URI(例如, `https://help.kusto.windows.net`
* Azure 的目錄服務終結點`https://login.microsoftonline.com/TENANT/`為 。

Azure`TENANT`活動目錄租戶 ID 或名稱在哪裡。 例如,在 Microsoft 租戶下建立的服務`https://login.microsoftonline.com/microsoft.com/`可以使用 。 或者,對於僅使用者身份驗證,可以`https://login.microsoftonline.com/common/`改為請求。

> [!NOTE]
> 在國家雲中運行時,Azure 活動目錄服務終結點會更改。
> 要更改將使用的終結點,將環境變數`AadAuthorityUri`設置為所需的 URI。

有關更多資訊,請參閱[身份驗證概述](../../management/access-control/index.md)和 Azure[活動目錄身份驗證指南](../../management/access-control/how-to-authenticate-with-aad.md)。