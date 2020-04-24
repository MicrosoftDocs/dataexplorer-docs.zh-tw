---
title: Kusto.Ingest 參考 - 攝取權限 - Azure 數據資源管理器 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Kusto.ingest 引用 - 引入許可權。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 198d562f880b74f6df4c6318f72213ee865f8f28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502885"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto.Ingest 參考 - 攝取權限
本文介紹了需要為服務設置哪些許可權才能`Native`引入工作。



## <a name="prerequisites"></a>Prerequisites
* 本文指導如何使用[Kusto 控制命令](../../management/security-roles.md)檢視並修改 Kusto 服務和資料庫上的授權設定
* 以下 AAD 應用程式在以下範例中用作範例主體:
    * 測試 AAD 應用程式 (2a904276-1234-5678-9012-66fc53add60b;microsoft.com)
    * Kusto 內部攝取 AAD 應用程式 (76263cdb-1234-5678-9012-545644e9c404;microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>排列擷取的引入權限模型
在[IKustoQueuedinestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)中定義,此模式限制對Kusto服務的用戶端代碼依賴項。 引入是通過將 Kusto 引入消息發佈到 Azure 佇列來執行的,而 Azure 佇列又從 Kusto 數據管理 (又名 kusto 數據管理 )獲取。 攝入)服務。 任何中間存儲專案將由收錄用戶端使用 Kusto 資料管理服務分配的資源創建。<BR>

下圖概述了與 Kusto 的佇列引入用戶端互動:<BR>

![alt 文字](../images/queued-ingest.jpg "排隊攝")

### <a name="permissions-on-the-engine-service"></a>引擎服務的權限
為了有資格將數據引入資料庫`T1``DB1`的表,必須授權執行引入操作的主體。
所需的許可權等級最小`Database Ingestor`,`Table Ingestor`並且 可以相應地將數據引入到資料庫中的所有現有表中或特定現有表中。
如果需要創建表,`Database User`或者還必須分配更高的訪問角色。


|角色 |PrincipalType    |主體顯示名稱
|--------|------------|------------
|資料庫 = 攝錄者 |AAD 應用程式 |測試應用程式(應用 ID: 2a904276-1234-5678-9012-66fc53add60b)
|表 = 攝錄 |AAD 應用程式 |測試應用程式(應用 ID: 2a904276-1234-5678-9012-66fc53add60b)

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`主體(Kusto 內部引入應用)不可修改地映射`Cluster Admin`到 角色,從而授權將數據引入到任何表(即 Kusto 託管的引入管道上發生的情況)。

向 AAD`DB1``Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)`應用`T1`授予資料庫 或表所需的許可權如下所示:
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```

