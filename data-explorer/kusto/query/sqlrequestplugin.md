---
title: sql_request 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 sql_request 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a8a0aae8732104ee64630c1fddb4d563cb542351
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94417552"
---
# <a name="sql_request-plugin"></a>sql_request 外掛程式

::: zone pivot="azuredataexplorer"

`sql_request`外掛程式會將 SQL 查詢傳送至 SQL Server 的網路端點，並傳回結果中的第一個資料列集。
查詢可能會傳回一個以上的資料列集，但 Kusto 查詢的其餘部分只會提供第一個資料列集。

## <a name="syntax"></a>Syntax

  `evaluate``sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *SqlParameters* [ `,` *Options* ]]`)`

## <a name="arguments"></a>引數

* *ConnectionString* ： `string` 表示指向 SQL Server 網路端點之連接字串的常值。 查看 [有效的驗證方法](#authentication) ，以及如何指定 [網路端點](#specify-the-network-endpoint)。

* *SqlQuery* ： `string` 常值，表示要對 SQL 端點執行的查詢。 必須傳回一或多個資料列集，但 Kusto 查詢的其餘部分只能使用第一個資料列集。

* *SqlParameters* ：類型的常數值 `dynamic` ，這個值會保存要傳遞做為參數的索引鍵/值組和查詢。 選擇性。
  
* *選項* ：類型的常數值 `dynamic` ，這個值會將更高階的設定保留為索引鍵/值組。 目前，只能 `token` 設定，以傳遞轉送至 SQL 端點的呼叫端所提供的 Azure AD 存取權杖進行驗證。 選擇性。

## <a name="examples"></a>範例

下列範例會將 SQL 查詢傳送至 Azure SQL DB 資料庫。 它會從抓取所有的記錄 `[dbo].[Table]` ，然後在 Kusto 端處理結果。 驗證會重複使用呼叫使用者的 Azure AD token。 

> [!NOTE]
> 此範例不應該做為建議，以這種方式篩選或投影資料。 由於 Kusto 優化器目前未嘗試優化 Kusto 與 SQL 之間的查詢，因此應將 SQL 查詢視為可傳回最小的資料集。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

下列範例與上一個範例相同，不同之處在于 SQL 驗證是透過使用者名稱/密碼來完成。 為了機密性，我們在這裡使用模糊字串。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Initial Catalog=Fabrikam;'
    h'User ID=USERNAME;'
    h'Password=PASSWORD;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

下列範例會將 SQL 查詢傳送至 Azure SQL DB 資料庫，以從中取出所有記錄 `[dbo].[Table]` ，同時附加另一個資料 `datetime` 行，然後在 Kusto 端處理結果。
它會指定 `@param0` 要在 SQL 查詢中使用 () 的 sql 參數。

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

## <a name="authentication"></a>驗證

Sql_request 外掛程式支援 SQL Server 端點的三種驗證方法：

### <a name="azure-ad-integrated-authentication"></a>Azure AD 整合式驗證 

`Authentication="Active Directory Integrated"`

  Azure AD 整合式驗證是慣用的方法。 這個方法會透過 Azure AD 來驗證使用者或應用程式，以 Kusto。 然後使用相同的權杖來存取 SQL Server 的網路端點。

### <a name="usernamepassword-authentication"></a>使用者名稱/密碼驗證

`User ID=...; Password=...;`

  當 Azure AD 整合式驗證無法完成時，會提供使用者名稱和密碼驗證支援。 可能的話，請盡可能避免使用這個方法，因為秘密資訊是透過 Kusto 傳送。

### <a name="azure-ad-access-token"></a>Azure AD 存取權杖

`dynamic({'token': h"eyJ0..."})`

   藉由 Azure AD 存取權杖驗證方法，呼叫端會產生存取權杖，此權杖會由 Kusto 轉送至 SQL 端點。 連接字串不應包含驗證資訊 `Authentication` ，例如、 `User ID` 或 `Password` 。 相反地，會 `token` 在 `Options` sql_request 外掛程式的引數中，以屬性形式傳遞存取權杖。
     
> [!WARNING]
> 包含機密資訊的連接字串和查詢，以及應受到防護的資訊，都應該經過模糊處理，以從任何 Kusto 追蹤中省略。
> 如需詳細資訊，請參閱 [模糊字串常](scalar-data-types/string.md#obfuscated-string-literals)值。

## <a name="encryption-and-server-validation"></a>加密和伺服器驗證

基於安全性考慮，連接到 SQL Server 網路端點時，會強制執行下列連接屬性。

* `Encrypt` 設定為 `true` 無條件。
* `TrustServerCertificate` 設定為 `false` 無條件。

因此，必須使用有效的 SSL/TLS 伺服器憑證來設定 SQL Server。

## <a name="specify-the-network-endpoint"></a>指定網路端點

在連接字串中指定 SQL 網路端點是必要的。
適當的語法為：

`Server``=` `tcp:` *FQDN* [ `,` *埠* ]

其中：

* *FQDN* 是端點的完整功能變數名稱。
* *Port* 是端點的 TCP 埠。 預設 `1433` 會假設為。

> [!NOTE]
> 不支援其他指定網路端點的形式。
> 例如， `tcp:` 即使在以程式設計方式使用 SQL 用戶端程式庫時，還是可以省略前置詞。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
