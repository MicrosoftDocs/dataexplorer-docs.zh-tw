---
title: Kusto 內嵌參考-內嵌許可權-Azure 資料總管 |Microsoft Docs
description: 本文說明 Kusto 的內嵌參考-Azure 資料總管中的內嵌許可權。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ec3b91056a4cfc584c0d7168549864b2b50ef5ae
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617913"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto。內嵌參考-內嵌許可權
本文說明在您的服務上設定哪些許可權，以便進行`Native`內嵌作業。


## <a name="prerequisites"></a>Prerequisites
* 若要在 Kusto 服務和資料庫上查看及修改授權設定，請參閱[Kusto 控制命令](../../management/security-roles.md) 

## <a name="references"></a>參考
* 下列 AAD 應用程式會用來作為下列範例中的範例主體：
    * 測試 AAD 應用程式（2a904276-1234-5678-9012-66fc53add60b; microsoft.com）
    * Kusto 內部內嵌 AAD 應用程式（76263cdb-1234-5678-9012-545644e9c404; microsoft.com）

## <a name="ingestion-permission-model-for-queued-ingestion"></a>已排入佇列的內嵌許可權模型
此模式定義于[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)中，會限制 Azure 資料總管服務的用戶端程式代碼相依性。 藉由將 Kusto 的內嵌訊息張貼至 Azure 佇列來完成內嵌作業。 佇列是從 Azure 資料總管服務取得。 It'ss 也稱為「內嵌服務」。  內嵌用戶端會使用 Azure 資料總管服務所配置的資源來建立任何中繼儲存體成品。

下圖概述已排入佇列的內嵌用戶端與 Kusto 的互動：<BR>

:::image type="content" source="../images/queued-ingest.jpg" alt-text="已排入佇列-內嵌":::

### <a name="permissions-on-the-engine-service"></a>引擎服務的許可權
若要限定將資料內嵌到`T1`資料庫`DB1`上的資料表，執行內嵌作業的主體必須具有授權。
最低必要許可權層級`Database Ingestor`是`Table Ingestor` ，而且可以將資料內嵌至資料庫中的所有現有資料表或特定現有的資料表。
如果需要建立資料表， `Database User`則也必須指派較高的存取角色。


|[角色] |PrincipalType    |PrincipalDisplayName
|--------|------------|------------
|`Database Ingestor` |AAD 應用程式 |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor` |AAD 應用程式 |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`主體（Kusto 內部內嵌應用程式）會, 對應至`Cluster Admin`角色，進而授權將資料內嵌到任何資料表。 這就是 Kusto 管理的內嵌管線上所發生的情況。

將資料庫`DB1`或資料表`T1`的必要許可權授與 AAD `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)`應用程式會如下所示：
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```
