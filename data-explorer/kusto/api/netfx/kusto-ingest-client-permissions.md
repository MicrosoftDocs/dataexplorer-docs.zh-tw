---
title: Kusto 內嵌的許可權-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Kusto 內嵌許可權。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fd516b7201c5e857417ca13bade668f32f25161
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226154"
---
# <a name="kustoingest---ingestion-permissions"></a>Kusto。內嵌-內嵌許可權

本文說明在您的服務上設定哪些許可權，以便進行內嵌 `Native` 作業。

## <a name="prerequisites"></a>先決條件

* 若要在 Kusto 服務和資料庫上查看及修改授權設定，請參閱[Kusto 控制命令](../../management/security-roles.md)。

* 在下列範例中，用來做為範例主體的 Azure Active Directory （Azure AD）應用程式：
    * 測試 Azure AD App （2a904276-1234-5678-9012-66fc53add60b; microsoft.com）
    * Kusto 內部內嵌 Azure AD App （76263cdb-1234-5678-9012-545644e9c404; microsoft.com）

## <a name="ingestion-permission-mode-for-queued-ingestion"></a>已排入佇列的內嵌許可權模式

內嵌許可權模式定義于[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)中。 此模式會限制 Azure 資料總管服務上的用戶端程式代碼相依性。 藉由將 Kusto 的內嵌訊息張貼至 Azure 佇列來完成內嵌作業。 佇列（也稱為「內嵌服務」）是從 Azure 資料總管服務取得。 內嵌用戶端會使用 Azure 資料總管服務所配置的資源來建立中繼儲存體成品。

此圖概述已排入佇列的內嵌用戶端與 Kusto 的互動。

:::image type="content" source="../images/queued-ingest.jpg" alt-text="已排入佇列-內嵌":::

### <a name="permissions-on-the-engine-service"></a>引擎服務的許可權

若要限定將資料內嵌到 `T1` 資料庫上的資料表 `DB1` ，執行內嵌作業的主體必須具有授權。
最低必要許可權層級是 `Database Ingestor` ，而且 `Table Ingestor` 可以將資料內嵌至資料庫中的所有現有資料表或特定現有的資料表。
如果需要建立資料表，則 `Database User` 也必須指派較高的存取角色。


|角色                 |PrincipalType        |PrincipalDisplayName
|---------------------|---------------------|------------
|`Database Ingestor`  |Azure AD 應用程式 |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor`     |Azure AD 應用程式 |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion Azure AD App (76263cdb-1234-5678-9012-545644e9c404)`主體（Kusto 內部內嵌應用程式）會, 對應至 `Cluster Admin` 角色。 因此，它會獲得授權，將資料內嵌到任何資料表。 這就是 Kusto 管理的內嵌管線上所發生的情況。

將資料庫或資料表的必要許可權授 `DB1` `T1` 與 Azure AD App 如下所 `Test App (2a904276-1234-5678-9012-66fc53add60b in Azure AD tenant microsoft.com)` 示：

```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
```
