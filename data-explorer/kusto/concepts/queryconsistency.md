---
title: 查詢一致性 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查詢一致性。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: b66540af2d2d4bef4571249474cd618e69eb2261
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523098"
---
# <a name="query-consistency"></a>查詢一致性

Kusto 支援兩個查詢一致性模型:**強**和**弱**。

強一致的查詢(預設值)具有"讀我更改"保證。 發送控制命令並收到成功完成該命令的正確認的用戶端將得到保證,緊隨其後的任何查詢都將遵守該命令的結果。

弱一致的查詢(必須由用戶端顯式啟用)不保證。 進行查詢的用戶端可能會觀察到一些延遲(通常為1-2分鐘)之間的更改和反映這些更改的查詢。

弱一致的查詢的優點是減少了處理資料庫更改的群集節點上的負載。 通常,建議客戶首先嘗試強一致的模型,並切換到使用弱一致性(如果絕對需要)。

切換到弱一致性的查詢是通過在進行 REST `queryconsistency` [API 調用](../api/rest/request.md)時設置屬性來完成的。 Kusto .NET 用戶端的使用者也可以將其設置在[Kusto 連接字串](../api/connection-strings/kusto.md)中,也可以將其設置為[用戶端請求屬性](../api/netfx/request-properties.md)中的標誌。