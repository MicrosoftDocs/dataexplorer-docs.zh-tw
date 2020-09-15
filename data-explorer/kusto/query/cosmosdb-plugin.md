---
title: cosmosdb_sql_request 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 cosmosdb_sql_request 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 09/11/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 035705e0a15ab686af16b9e8b2a00268db21d6a2
ms.sourcegitcommit: c140bc3bc984f861df0b85e672d2e685e6659a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90109976"
---
# <a name="cosmosdb_sql_request-plugin"></a>cosmosdb_sql_request 外掛程式

::: zone pivot="azuredataexplorer"

`cosmosdb_sql_request`外掛程式會將 SQL 查詢傳送至 COSMOS DB sql 網路端點，並傳回查詢的結果。 此外掛程式主要是設計用來查詢小型資料集，例如，使用儲存在 [Azure Cosmos DB](/azure/cosmos-db/)中的參考資料來充實資料。

## <a name="syntax"></a>語法

`evaluate``cosmosdb_sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *SqlParameters* [ `,` *Options*]]`)`

## <a name="arguments"></a>引數

|引數名稱 | 描述 | 必要條件/選擇性 | 
|---|---|---|
| *ConnectionString* | `string`表示指向要查詢之 Cosmos DB 集合的連接字串的常值。 它必須包含 *>accountendpoint*、 *Database*和 *Collection*。 如果主要金鑰是用於驗證，它可能會包含 *AccountKey* 。 <br> **範例：** `'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/ ;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;'`| 必要 |
| *SqlQuery*| `string`表示要執行之查詢的常值。 | 必要 |
| *SqlParameters* | 型別的常數值 `dynamic` ，這個值會保存索引鍵/值組，以傳遞做為參數和查詢。 參數名稱的開頭必須是 `@` 。 | 選擇性 |
| *選項* | 型別的常數值 `dynamic` ，這個值會以索引鍵/值組的形式保存更多 advanced 設定。 | 選擇性 |
|| ----*支援的選項設定包括：*-----
|      `armResourceId` | 從 Azure Resource Manager 取出 API 金鑰 <br> **範例：** `/subscriptions/a0cd6542-7eaf-43d2-bbdd-b678a869aad1/resourceGroups/ cosmoddbresourcegrouput/providers/Microsoft.DocumentDb/databaseAccounts/cosmosdbacc`| 
|  `token` | 提供用來向 Azure Resource Manager 驗證的 Azure AD 存取權杖。
| `preferredLocations` | 控制要查詢資料的區域。 <br> **範例：** `['East US']` | |  

## <a name="set-callout-policy"></a>設定標注原則

外掛程式會將 Cosmos DB 的標注。 確定叢集的注標[原則](../management/calloutpolicy.md)可讓您對目標 CosmosDbUri 進行類型的呼叫 `cosmosdb` 。 *CosmosDbUri*

下列範例示範如何定義 Cosmos DB 的標注原則。 建議您將它限制在 () 的特定 `my_endpoint1` 端點 `my_endpoint2` 。

```kusto
[
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint1.documents.azure.com",
    "CanCall": true
  },
  {
    "CalloutType": "CosmosDB",
    "CalloutUriRegex": "my_endpoint2.documents.azure.com",
    "CanCall": true
  }
]
```

下列範例顯示 CalloutType 的 alter callout 原則命令 `cosmosdb` *CalloutType*

```kusto
.alter cluster policy callout @'[{"CalloutType": "cosmosdb", "CalloutUriRegex": "\\.documents\\.azure\\.com", "CanCall": true}]'
```

## <a name="examples"></a>範例

### <a name="query-cosmos-db"></a>查詢 Cosmos DB

下列範例會使用 *cosmosdb_sql_request* 外掛程式來傳送 SQL 查詢，以使用其 sql API 從 Cosmos DB 提取資料。

```kusto
evaluate cosmosdb_sql_request(
  'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
  'SELECT * from c')
```

### <a name="query-cosmos-db-with-parameters"></a>具有參數的查詢 Cosmos DB

下列範例會使用 SQL 查詢參數，並查詢替代區域中的資料。 如需詳細資訊，請參閱 [`preferredLocations`](/azure/cosmos-db/tutorial-global-distribution-sql-api?tabs=dotnetv2%2Capi-async#preferred-locations)。

```kusto
evaluate cosmosdb_sql_request(
    'AccountEndpoint=https://cosmosdbacc.documents.azure.com:443/;Database=MyDatabase;Collection=MyCollection;AccountKey=' h'R8PM...;',
    "SELECT c.id, c.lastName, @param0 as Column0 FROM c WHERE c.dob >= '1970-01-01T00:00:00Z'",
    dynamic({'@param0': datetime(2019-04-16 16:47:26.7423305)}),
    dynamic({'preferredLocations': ['East US']}))
| where lastName == 'Smith'
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
